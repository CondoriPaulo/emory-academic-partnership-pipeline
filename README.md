# emory-academic-partnership-pipeline
Azure data pipeline tracking nursing school partnerships through clinical rotations, hiring, and retention at Emory Healthcare


# Emory Academic Partnership Pipeline

> Azure-based data pipeline tracking nursing school partnerships through clinical rotations, hiring, and retention at Emory Healthcare.

**Capstone Project — Emory University AI & Data Leadership Program (AIDL)**

---

## 🎯 The Problem

Emory Healthcare partners with 74+ nursing schools but lacked a unified view of which schools produce students who:
- Complete clinical rotations
- Get hired after graduation
- Stay long-term as nurses

## 🏗️ Solution Architecture

A fully automated, HIPAA-aligned data pipeline ingesting from 4 sources into a Power BI dashboard.

<img width="155" height="716" alt="image" src="https://github.com/user-attachments/assets/166acce1-88df-4deb-afa9-b971731a7635" /> <img width="231" height="618" alt="image" src="https://github.com/user-attachments/assets/65f56c52-618d-4c85-adab-9e0b681f7e8d" />  <img width="172" height="636" alt="image" src="https://github.com/user-attachments/assets/6abb3aff-cd46-4192-8c96-fce8ad97366c" />  <img width="815" height="509" alt="image" src="https://github.com/user-attachments/assets/888e0827-b711-465a-9414-46d70e0e7321" />  <img width="258" height="716" alt="image" src="https://github.com/user-attachments/assets/be8aef2a-2fa2-4e54-95bd-30de075766c9" />






**Stack:**
- **Azure Function Apps** (Python) — Data cleaning & normalization
- **Azure Logic Apps** — Orchestration & SQL UPSERTs
- **Azure SQL Database** — Star schema warehouse
- **Azure AI Document Intelligence** — Deterministic OCR for PDF sources
- **Power BI** — DirectQuery dashboard with DAX measures

---

## 📦 Repository Index

This project consists of 4 Azure Function App repositories, each handling one data source:

| # | Function App | Source Data | Repository |
|---|---|---|---|
| 1 | ACEMAPP Rotation Parser | ~7,500 clinical rotation records (Excel) | [→ View Repo](https://github.com/CondoriPaulo/v2-AcemappRotationRequestFunc) |
| 2 | Vizient Nurse Processor | ~5,225 hire/termination records (Excel) | [→ View Repo](https://github.com/CondoriPaulo/v2-VizientNurseHiresAndExitsFunc) |
| 3 | Affiliated Schools Parser | 74 partner schools (PDF, OCR) | [→ View Repo](https://github.com/CondoriPaulo/affilatedSchools-v2-ParseAffiliatedSchools) |
| 4 | NCLEX Normalizer | NCLEX pass-rate data (PDF, complex headers) | [→ View Repo](https://github.com/CondoriPaulo/NCLEX-normalizierfunc) |

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
