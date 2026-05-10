# emory-academic-partnership-pipeline
Azure data pipeline tracking nursing school partnerships through clinical rotations, hiring, and retention at Emory Healthcare


# Emory Academic Partnership Pipeline

> Azure-based data pipeline tracking nursing school partnerships through clinical rotations, hiring, and retention at Emory Healthcare.

**Capstone Project — Emory University AI & Data Leadership Program (AIDL)**

---

## 🎯 The Problem

Emory Healthcare partners with 74+ nursing schools, but lacks a unified view of which schools produce students who:
- Complete clinical rotations
- Get hired after graduation
- Stay long-term as nurses

## 🏗️ Solution Architecture

A fully automated, HIPAA-aligned data pipeline ingesting from 4 sources into a Power BI dashboard.


### How It Works

The pipeline handles two distinct source types — **PDFs** and **structured files (Excel/CSV)** — using a consistent pattern: ingestion → cleaning → JSON normalization → SQL load → Power BI.

#### 📄 PDF Sources (Affiliated Schools, NCLEX)

1. **File drop** — A new PDF lands in OneDrive/SharePoint, triggering an Azure Logic App
2. **OCR extraction** — Logic App sends the file to **Azure AI Document Intelligence**, which returns a raw JSON response (~30,000 lines) of layout, tables, and text
3. **Normalization** — The raw JSON is passed to a custom **Azure Function App (Python)** that parses it into a clean, row-oriented JSON structure with consistent field names and human-readable values
4. **Database load** — Logic App iterates through the cleaned JSON and **UPSERTs** each row into the corresponding Azure SQL table using `MERGE` statements

> **Why a Function App for normalization?** Document Intelligence returns positional/layout data, not relational rows. We studied the source documents to understand their structure, then built deterministic Python parsers to convert the OCR output into clean rows that match the SQL schema.

#### 📊 Structured Sources (ACEMAPP, Vizient)

1. **File drop** — Excel/CSV file lands in OneDrive/SharePoint, triggering a Logic App
2. **Cleaning & transformation** — File is sent directly to a Python **Azure Function App** (no OCR needed) that:
   - Profiles the data
   - Strips control characters (e.g. `\t` prefixes in Vizient data)
   - Resolves column boundary issues
   - Converts the cleaned data into a normalized JSON structure
3. **Database load** — Logic App iterates through the JSON and inserts each row into the appropriate SQL table

### Architectural Rule: Separation of Concerns

A core design principle enforced across all four pipelines:

| Layer | Responsibility |
|---|---|
| **Function App (Python)** | Reads → cleans → returns JSON. **Never writes to SQL.** |
| **Logic App** | Orchestrates flow, handles SQL UPSERTs, manages retries |
| **Azure SQL** | Stores normalized data in star-schema tables |
| **Power BI** | Reconciles cross-source data (e.g. fuzzy school name matching) via DirectQuery + DAX |

This separation makes each component independently testable and replaceable — the Function App can be reused for other Logic Apps, and the SQL load logic stays orchestration-side where it belongs.

---

**Stack:**
- **Azure Function Apps** (Python) — Data cleaning & normalization
- **Azure Logic Apps** — Orchestration & SQL UPSERTs
- **Azure SQL Database** — Star schema warehouse
- **Azure AI Document Intelligence** — Deterministic OCR for PDF sources
- **Power BI** — DirectQuery dashboard with DAX measures


NCLEX Logic App Flow :
<img width="155" height="716" alt="image" src="https://github.com/user-attachments/assets/166acce1-88df-4deb-afa9-b971731a7635" /> 


Vizient Logic App Flow:
<img width="231" height="618" alt="image" src="https://github.com/user-attachments/assets/65f56c52-618d-4c85-adab-9e0b681f7e8d" /> 


Affiliated School Logic App Flow:
<img width="172" height="636" alt="image" src="https://github.com/user-attachments/assets/6abb3aff-cd46-4192-8c96-fce8ad97366c" />  


Acemapp Logic App Flow:
<img width="815" height="509" alt="image" src="https://github.com/user-attachments/assets/888e0827-b711-465a-9414-46d70e0e7321" />  <img width="258" height="716" alt="image" src="https://github.com/user-attachments/assets/be8aef2a-2fa2-4e54-95bd-30de075766c9" />



---

## 📦 Repository Index

This project consists of 4 Azure Function App repositories, each handling one data source:

| # | Function App | Source Data | Repository |
|---|---|---|---|
| 1 | ACEMAPP Rotation Parser | ~7,500 clinical rotation records (Excel) | [→ View Repo](https://github.com/CondoriPaulo/ACEMAPP) |
| 2 | Vizient Nurse Processor | ~5,225 hire/termination records (Excel) | [→ View Repo](https://github.com/CondoriPaulo/VIZIENT) |
| 3 | Affiliated Schools Parser | 74 partner schools (PDF, OCR) | [→ View Repo](https://github.com/CondoriPaulo/affiliatedschoolsFunc) |
| 4 | NCLEX Normalizer | NCLEX pass-rate data (PDF, complex headers) | [→ View Repo](https://github.com/CondoriPaulo/NCLEX-NormalizerFunc) |

---

## 🧱 Database Schema

Star schema designed for analytical queries:

- **Dimension tables:** `dim_school`, `dim_site`, `dim_unit`
- **Fact tables:** `fact_rotation`, `fact_nurse`
- **Reference tables:** `ref_nclex`

---

## 🔑 Design Principles

- **Separation of concerns** — Functions return JSON only; Logic Apps handle SQL writes
- **Idempotent loads** — All inserts use MERGE/UPSERT patterns
- **Deterministic over generative** — Azure Document Intelligence chosen over LLMs for HIPAA compliance and auditability
- **DirectQuery over Import** — Live data access for the entire team without manual refresh
- **Raw data preservation** — All sources stored unchanged before transformation

---

## 📊 Results

- 4 automated pipelines + 2 manual SQL loads consolidated into a single Power BI dashboard
- Reduced manual data reconciliation effort across 5+ disparate sources
- Enabled leadership to answer the core partnership question: *Which schools produce students who complete rotations, get hired, and stay?*

---

## 👥 Team

**Technical Lead:** Paulo Sergio Condori  
**Team:** Elizabeth Askin, Leon Le, Katie Wang, Taylor Norfleet  
**Mentors:** Kevin Kim, Riyan Shah

---

## 🔗 Connect

- 💼 [LinkedIn](https://www.linkedin.com/in/paulosergio1996/)
- 🐙 [GitHub](https://github.com/CondoriPaulo)
