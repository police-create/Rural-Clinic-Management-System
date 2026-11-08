# Rural Clinic Management System (RCMS)
### Production-Quality Healthcare Administration Platform for Rural Clinics in India
**గ్రామీణ క్లినిక్ నిర్వహణ వ్యవస్థ | ग्रामीण क्लिनिक प्रबंधन प्रणाली**

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg)]()
[![Tests](https://img.shields.io/badge/tests-24%20passed-success.svg)]()
[![TypeScript](https://img.shields.io/badge/TypeScript-5.5-blue.svg)]()
[![React](https://img.shields.io/badge/React-18-blue.svg)]()
[![Node](https://img.shields.io/badge/Node.js-20%2B-green.svg)]()
[![MongoDB](https://img.shields.io/badge/MongoDB-7.0-green.svg)]()
[![License](https://img.shields.io/badge/license-MIT-blue.svg)]()

---

## 1. Executive Summary & Problem Statement

Primary and secondary healthcare clinics in rural India (such as Primary Health Centres [PHCs] and Community Health Centres [CHCs] in Andhra Pradesh, Telangana, and other states) face critical operational challenges:
- **Paper Register Fragility:** Patient histories are scattered across physical notebooks, leading to lost medical histories and repeated diagnostic expenses.
- **Medicine Expiry & Wastage:** Rural pharmacies lack First Expired First Out (FEFO) controls, resulting in stockouts of essential antibiotics and expired stock wastage.
- **Doctor Double-Booking:** Overlapping handwritten appointments lead to extended patient wait times and disorderly clinic queues.
- **Financial Reconciliation Gaps:** Cash and UPI payments lack unified receipting and daily settlement audit trails.
- **Connectivity Constraints:** Rural clinics suffer from intermittent broadband connections, demanding lightweight assets, offline status alerts, and multi-lingual accessibility (Telugu, Hindi, English).

The **Rural Clinic Management System (RCMS)** provides an autonomous, full-stack, enterprise-grade healthcare management solution designed specifically to solve these challenges with 100% genuine database persistence, strict RBAC permissions, and low-bandwidth optimization.

---

## 2. Key Architectural Highlights & Engineering Standards

- **Integer Paise Money Management:** All financial transactions, consultation charges, taxes, and balances are stored as integer paise (e.g. ₹100.00 = `10000` paise) to prevent floating-point rounding errors. Deterministic half-up rounding is applied to all GST calculations.
- **Asia/Kolkata Timezone Standards:** While timestamps are recorded in UTC, all clinic metrics, queues, and reports are computed with exact midnight cutovers in `Asia/Kolkata` (IST = UTC+5:30).
- **Atomic Identifier Generators:** Concurrency-safe atomic sequence counters generate unique identifiers:
  - Patients: `PT-2026-000001`
  - Appointments: `APT-2026-000001`
  - Queue Tokens: `Q-001`
  - Consultations: `CNS-2026-000001`
  - Prescriptions: `RX-2026-000001`
  - Lab Orders: `LAB-2026-000001`
  - Invoices: `INV-2026-000001`
  - Payments: `PAY-2026-000001`
- **Conflict Prevention Engine:** Doctor appointment slots are validated against clinic operating hours and protected by compound database indexes against double booking.
- **Batch-Level FEFO Dispensing:** Pharmacy stock is tracked per batch with expiry dates. The dispensing engine automatically allocates medicines from the nearest-expiry batch (First Expired, First Out) while strictly blocking expired stock.
- **Spreadsheet Formula Injection Defense (CWE-1236):** All CSV report exports sanitize fields starting with `=`, `+`, `-`, `@`, `\t`, `\r` to protect clinic computers against malicious formula execution.
- **Multi-Lingual Localization:** Full interface internationalization supporting English, Telugu (తెలుగు), and Hindi (हिंदी).
- **Embedded Database Fallback:** The backend automatically spins up an embedded in-memory MongoDB engine if no external MongoDB instance is running, ensuring the system runs out-of-the-box on any machine.

---

## 3. Technology Stack

### Frontend Layer
- **Framework:** React 18 with Vite and TypeScript
- **Styling:** Tailwind CSS with soft-blue healthcare palette and restrained teal accents
- **Routing:** React Router v6 with Role-Based Route Guards
- **Data Fetching:** TanStack React Query v5 with automatic cache invalidation
- **Charts & Visualizations:** Recharts (Patient intake trends, Revenue collection trends)
- **Icons:** Lucide React
- **Internationalization:** i18next & react-i18next (English, Telugu, Hindi)

### Backend Layer
- **Runtime:** Node.js (v20+) with Express.js and TypeScript
- **Database Engine:** MongoDB 7.0 via Mongoose ODM
- **Validation:** Zod schemas shared between frontend and backend
- **Authentication:** JWT (Access & Refresh tokens) with secure HttpOnly SameSite cookies
- **Security:** Helmet headers, Express Rate Limiter, CORS, sensitive data log redaction
- **Logging:** Pino with structured audit logging

### Testing & DevOps
- **Test Runners:** Vitest v2.1.9 with Supertest and React Testing Library
- **Containerization:** Docker & Docker Compose with MongoDB health checks
- **CI Pipeline:** GitHub Actions automated build and test runner

---

## 4. Role-Based Access Control (7 Distinct Roles)

| Role | Default User | Default Password | Primary Clinical & Administrative Privileges |
|---|---|---|---|
| **ADMIN** | `admin` | `ClinicPass@2026` | Full system access, staff management, clinic settings, audit logs |
| **DOCTOR** | `doctor1` | `ClinicPass@2026` | Clinical consultations, vital reviews, diagnosis, Rx issuance, lab orders |
| **NURSE** | `nurse` | `ClinicPass@2026` | Queue triage, patient vitals recording, BMI alerts, queue calling |
| **RECEPTIONIST** | `receptionist` | `ClinicPass@2026` | Patient registration, appointment booking, queue token issuance |
| **PHARMACIST** | `pharmacist` | `ClinicPass@2026` | Formulary inventory, stock batch receipt, FEFO prescription dispensing |
| **LAB_TECH** | `labtech` | `ClinicPass@2026` | Sample collection, diagnostic test execution, result entry, abnormal flags |
| **BILLING** | `billing` | `ClinicPass@2026` | Invoice generation, partial/full payment recording, daily cash/UPI settlement |

*Note: For academic evaluation, the login screen includes 1-click quick-switch buttons for all 7 roles.*

---

## 5. Complete 12-Module Overview

1. **Patient Management:** Registration with automatic `PT-2026-XXXXXX` IDs, duplicate detection by phone/name/age, village search, allergies, chronic conditions, and longitudinal medical history timeline.
2. **Appointment Scheduling:** 15-minute slot generation based on clinic working hours, doctor selection, conflict detection preventing overlapping bookings, and calendar views.
3. **Queue & Triage:** Daily outpatient token issuance (`Q-001`), priority tags (Normal, Urgent, Emergency), nurse triage vitals recording (BP, pulse, temp, SpO2, height, weight), automated BMI calculation, and non-diagnostic attention alerts.
4. **Doctor Consultation Workspace:** Patient medical history review, drug allergy warning banner, chief complaints, clinical observations, clinician diagnosis, treatment plan, and follow-up scheduling.
5. **Electronic Prescriptions:** Formulary medicine picker, dosage, frequency, duration, route, instructions, allergy warning checks, and professional printable Rx slips with clinic letterhead.
6. **Pharmacy & Medicine Inventory:** Medicine catalog, batch tracking with expiry dates, low-stock alerts, near-expiry warnings, FEFO dispensing, and append-only stock ledger.
7. **Diagnostic Laboratory:** Order creation, sample collection, test result recording with automated abnormal flags (LOW, NORMAL, HIGH, CRITICAL), and printable lab reports.
8. **Billing & Financial Records:** Invoicing in integer paise, consultation/medicine/lab line items, GST calculations, partial cash/UPI/card payments, remaining balances, and daily collection reports.
9. **Staff & Role Administration:** Staff registration, role assignment, account activation/deactivation, qualifications, and medical license numbers.
10. **Dashboard & Analytics:** Live KPI counters, 7-day registration and revenue collection Recharts, live queue status, and quick module actions.
11. **Reports & Exports:** 10 audit-ready reports (Patient, Appointment, Consultation, Prescription, Inventory, Expiry, Lab, Billing, Revenue, Staff Activity) with sanitized CSV export downloads.
12. **Clinic Settings & Security:** Clinic profile, working hours, consultation fee, GST configuration, and tamper-evident audit log viewer.

---

## 6. Quick Start & Setup Guide

### Prerequisites
- Node.js v20+ and npm v10+
- Git

### Option A: Standard Local Development (Zero External DB Setup Required!)
The application includes an embedded database runner that starts automatically:

```bash
# 1. Clone or open repository
cd "Rural Clinic Management System"

# 2. Build shared TypeScript package
npm run build:shared

# 3. Seed database with 27 patients, 3 doctors, staff, medicines, and sample records
npm run seed

# 4. Start backend server (Port 5000)
npm run dev:backend

# 5. In a second terminal, start frontend dashboard (Port 5173)
npm run dev:frontend
```

Open `http://localhost:5173` in your browser.

### Option B: Docker Compose Deployment

```bash
docker-compose up --build
```
- Frontend: `http://localhost:80`
- Backend API: `http://localhost:5000/api`
- MongoDB: `mongodb://localhost:27017`

---

## 7. Running Automated Test Suites

The project includes 24 automated unit, integration, and security tests:

```bash
# Run all backend tests (Security, Dates, Money, and Full Integration Lifecycle)
npm run test:backend

# Run frontend tests (Format utilities, StatCards, Accessible Modals)
npm run test:frontend
```

### Verified Test Summary:
- `src/tests/security.test.ts`: 3 tests (CWE-1236 Formula Injection Protection)
- `src/tests/date.test.ts`: 2 tests (Asia/Kolkata Boundaries & UTC Conversion)
- `src/tests/money.test.ts`: 4 tests (Integer Paise & Deterministic GST)
- `src/tests/integration.test.ts`: 10 tests (Full 10-step clinical lifecycle)
- `src/tests/format.test.ts`: 3 tests (UI Currency & Timezone formatting)
- `src/tests/components.test.tsx`: 2 tests (Component rendering & Modal accessibility)
**Total: 24 / 24 Tests Passing (100% Green)**

---

## 8. Five-Minute Faculty Demonstration Steps

1. **Login Page:** Point out the 1-click demo selector for all 7 roles.
2. **Receptionist:** Register a new patient (*Narasimha Rao*) -> show generated ID `PT-2026-000028` -> book an appointment slot -> check patient in -> token `Q-011` issued.
3. **Nurse:** Open **Queue & Triage** -> record vitals (BP `150/95`, Weight `80kg`, Height `170cm`) -> show automatic BMI calculation (`27.7 kg/m²`) and attention flag.
4. **Doctor:** Open **Consultations** -> notice **Drug Allergy Alert** -> enter diagnosis (*Hypertension*) -> prescribe *Amlodipine 5mg* -> order *Complete Blood Picture* -> complete consultation -> print electronic Rx slip.
5. **Pharmacist:** Open **Pharmacy** -> show FEFO batch allocation -> dispense prescription -> show stock decrement.
6. **Billing:** Open **Billing** -> show generated invoice in paise -> record partial cash payment (₹100) -> record remaining UPI payment (₹100) -> invoice marked `PAID`.
7. **Reports:** Open **Reports** -> export sanitized CSV -> view tamper-evident audit logs in **Clinic Settings**.

---

## 9. Academic Project Deliverables

- Complete source code in clean monorepo (`shared/`, `backend/`, `frontend/`)
- Architecture Specification: `docs/architecture/ARCHITECTURE.md`
- Automated Test Evidence: `docs/evidence/TEST_RESULTS.md`
- Viva Preparation Guide: `docs/VIVA_PREPARATION.md`
- Progress Log: `PROGRESS.md`

