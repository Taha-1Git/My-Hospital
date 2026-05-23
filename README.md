# 🏥 Ivor Paine Memorial Hospital — Database Management System

> A full-stack hospital management system built with **PHP** and **Microsoft SQL Server**, featuring a normalized relational database, ER/EER modeling, a sidebar-driven web UI, and 12 pre-built analytical reports.

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Database Design](#database-design)
  - [Tables Overview](#tables-overview)
  - [Relational Schema](#relational-schema)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation & Setup](#installation--setup)
- [Pages & Forms](#pages--forms)
- [Reports & Queries](#reports--queries)
- [Academic Context](#academic-context)

---

## Project Overview

The **Ivor Paine Memorial Hospital Database System** replaces a manual record-keeping process with a fully computerized solution. The system manages:

- Patient admissions, bed assignments, complaints, and treatment history
- Ward records, care units, and nursing staff allocation
- Consultant teams, doctor positions, experience history, and performance grades
- 12 analytical SQL reports covering clinical, administrative, and executive needs

---

## Features

| Area | Details |
|---|---|
| **Dashboard** | Live stats — total patients, admitted, doctors, consultants, nurses, wards, beds, treatment records; recent admissions table |
| **Patient Records** | Add/view patients; lookup by Patient No; full medical history per patient |
| **Ward Records** | View wards with day/night sisters, bed listings, care units |
| **Consultant Teams** | Browse consultant specialties and their full doctor team roster |
| **12 Reports** | Parameterised and static queries covering all hospital reporting needs |
| **Sidebar Navigation** | Persistent sidebar with section groupings for Records and Reports |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend / Templates | PHP 8.x with `render_header()` / `render_footer()` layout helpers |
| Styling | Custom CSS (`DM Sans` + `DM Serif Display` via Google Fonts) |
| Database | Microsoft SQL Server (`IvorHospitalDB`) |
| DB Driver | `sqlsrv` PHP extension (Windows SQL Server native driver) |
| Local Server | XAMPP / WAMP with SQL Server, or IIS + PHP |
| Query File | T-SQL (`queries.sql`) — all 12 queries with parameterised variants |

---

## Database Design

### Tables Overview

| Table | Description |
|---|---|
| `STAFF` | Base table for all hospital staff — doctors, nurses, and consultants share a `StaffNo` |
| `CONSULTANT` | Consultant specialty; each leads a doctor team |
| `DOCTOR` | Position, team membership (`ConsultantNo`), and join date |
| `NURSE` | Nurse type (`Day Sister`, `Night Sister`, `Staff Nurse`, `Non-Registered`) and ward/care-unit assignment |
| `WARD` | Ward name, specialty, and references to Day/Night Sister `StaffNo` |
| `CARE_UNIT` | Sub-unit within a ward; has a Staff Nurse in charge (`InChargeStaffNo`) |
| `BED` | Individual beds with unique hospital-wide numbers, linked to a ward |
| `PATIENT` | Demographics, current ward/care-unit/bed, doctor in charge, admission and discharge dates |
| `COMPLAINT` | Coded medical complaints |
| `TREATMENT` | Coded treatment types |
| `MEDICAL_HISTORY` | Links patient → complaint → treatment → doctor; records `DateStarted` and `DateEnded` |
| `EXPERIENCE_RECORD` | Previous positions held by a doctor at external establishments |
| `PERFORMANCE_RECORD` | Six-monthly consultant-assigned performance grades per doctor |

### Relational Schema

```
STAFF(StaffNo PK, Name)

CONSULTANT(StaffNo PK FK→STAFF, Specialty)

DOCTOR(StaffNo PK FK→STAFF, ConsultantNo FK→CONSULTANT, Position, DateJoinedTeam)

NURSE(StaffNo PK FK→STAFF, NurseType, WardName FK→WARD, CareUnitNo FK→CARE_UNIT)

WARD(WardName PK, Specialty, DaySisterStaffNo FK→STAFF, NightSisterStaffNo FK→STAFF)

CARE_UNIT(CareUnitNo PK, WardName FK→WARD, InChargeStaffNo FK→STAFF)

BED(BedNo PK, WardName FK→WARD)

PATIENT(PatientNo PK, Name, DateOfBirth, DateAdmitted, DateDischarged,
        CareUnitNo FK→CARE_UNIT, BedNo FK→BED, DoctorInCharge FK→DOCTOR)

COMPLAINT(ComplaintCode PK, Description)

TREATMENT(TreatmentCode PK, Description)

MEDICAL_HISTORY(PatientNo FK→PATIENT, ComplaintCode FK→COMPLAINT,
                TreatmentCode FK→TREATMENT, DoctorNo FK→DOCTOR,
                DateStarted, DateEnded)

EXPERIENCE_RECORD(DoctorNo FK→DOCTOR, FromDate, ToDate, Position, Establishment)

PERFORMANCE_RECORD(DoctorNo FK→DOCTOR, ConsultantNo FK→CONSULTANT, GradeDate, Grade)
```

> 📁 Full DDL with integrity constraints → `sql/create_tables.sql`  
> 📁 Seed/initial records → `sql/insert_data.sql`  
> 📁 ER/EER diagram → `docs/ER_Diagram.pdf`

---

## Project Structure

```
project/
│
├── index.php                    # Dashboard — live stats + recent patients table
│
├── assets/
│   └── style.css                # Full custom CSS (DM Sans, sidebar, cards, tables, pills)
│
├── config/
│   ├── db.php                   # SQL Server connection (sqlsrv); dbQuery(), dbExecute(), h()
│   └── layout.php               # render_header() / render_footer() — sidebar + topbar
│
├── pages/
│   ├── patient_record.php       # Patient form — lookup by Patient No; add new patient
│   ├── ward_record.php          # Ward record — select by Ward Name; beds & care-unit details
│   ├── consultant_team.php      # Consultant Team Record — lookup by Staff No
│   └── reports.php              # All 12 reports via ?q=N; parameterised forms for Q9–Q11
│
├── sql/
│   ├── create_tables.sql        # DDL: CREATE TABLE with all constraints
│   ├── insert_data.sql          # DML: initial seed data
│   └── queries.sql              # All 12 T-SQL report queries
│
└── docs/
    ├── ER_Diagram.pdf
    └── Relational_Schema.pdf
```

---

## Getting Started

### Prerequisites

- **PHP 8.0+** with the `sqlsrv` and `pdo_sqlsrv` extensions enabled
- **Microsoft SQL Server** (2019 / Express or later)
- **SQL Server Management Studio (SSMS)** — recommended for DB setup
- **XAMPP** or **WAMP** — place the project under `htdocs/project/`

### Installation & Setup

**1. Clone the repository**
```bash
git clone https://github.com/your-username/ivor-hospital-db.git
```

**2. Place under your web server root**
```
C:/xampp/htdocs/project/
```

**3. Configure the database connection**

Open `config/db.php` and update:
```php
define('DB_SERVER',   'YOUR_SERVER_NAME');  // e.g. DESKTOP-XXXXXXX or localhost\SQLEXPRESS
define('DB_NAME',     'IvorHospitalDB');
define('DB_USER',     'ivoruser');          // leave null to use Windows Authentication
define('DB_PASSWORD', 'YourPassword');
```

**4. Create the database in SSMS**
```sql
CREATE DATABASE IvorHospitalDB;
```

**5. Run the SQL scripts in order**

In SSMS, open and execute each file:
```
sql/create_tables.sql   -- creates all tables with integrity constraints
sql/insert_data.sql     -- populates initial records
```

**6. Open in your browser**
```
http://localhost/project/index.php
```

---

## Pages & Forms

| Page | URL | Description |
|---|---|---|
| Dashboard | `/index.php` | Live stats cards + recently admitted patients |
| Patient Record | `/pages/patient_record.php` | Search by Patient No; add new patient; view full medical history |
| Ward Record | `/pages/ward_record.php` | Select ward from dropdown; view sisters, beds, care units, patients |
| Consultant Team | `/pages/consultant_team.php` | Lookup by Staff No; view team, positions, and performance grades |
| All Reports | `/pages/reports.php` | Run any of the 12 reports; Q9–Q11 include input forms |

---

## Reports & Queries

All 12 reports are accessible from the sidebar and Reports page (`?q=1` through `?q=12`):

| # | Report | Type |
|---|---|---|
| Q1 | Consultants and the doctors in their team | Static |
| Q2 | Wards with sisters, care units, and staff nurses in charge | Static |
| Q3 | Patients with complaints, treatments, and treatment dates | Static |
| Q4 | Junior housemen, their patients, and care-unit staff nurse | Static |
| Q5 | Consultants with a unique specialty | Static |
| Q6 | Complaints, treatments given, and treating doctor's experience history | Static |
| Q7 | Patients with more than one complaint and their treatments | Static |
| Q8 | Patients grouped by treatment within complaint | Static |
| Q9 | Performance history for a particular doctor | Parameterised (Doctor No) |
| Q10 | Full medical details for a particular patient | Parameterised (Patient No) |
| Q11 | Treatments for a complaint between two dates, ordered by treatment | Parameterised (Complaint, Date Range) |
| Q12 | All staff positions with count of staff in each (doctors + nurses combined) | Static |

> 📁 All T-SQL source → `sql/queries.sql`

---

## Academic Context

| Field | Detail |
|---|---|
| **Course** | Database Systems / Information Systems (CS204) |
| **Case Study** | Ivor Paine Memorial Hospital |
| **Deliverables** | ER/EER Diagram · Relational Schema · Normalized Tables · DDL/DML SQL · 12 Queries · PHP Front End |
| **Normalization** | Up to 3NF applied across all tables |
| **Integrity Constraints** | Primary keys, foreign keys, NOT NULL, UNIQUE enforced via DDL |
| **DB Driver** | Microsoft `sqlsrv` PHP extension (native Windows SQL Server) |

---

## License

Developed for academic purposes. Not licensed for commercial use.

