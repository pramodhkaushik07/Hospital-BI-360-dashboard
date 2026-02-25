# 🏥 Hospital BI 360 Dashboard
### End-to-End Power BI Solution | Data Engineering · Advanced Analytics · Executive Reporting

---
## Dashboard Preview

<img width="1430" height="809" alt="image" src="https://github.com/user-attachments/assets/640b1641-56bb-47b5-94fe-fa9f718edecb" />


--- 
## 📌 Executive Summary

This project delivers a **production-grade, enterprise-scale Hospital Management Dashboard** built entirely in Microsoft Power BI. It consolidates fragmented clinical, operational, and financial data from 16 source tables into a unified 5-table analytical model — enabling real-time, decision-grade insights across patient care, doctor performance, bed occupancy, and hospital finance.

The dashboard is architected to mirror **SaaS-grade product standards**, featuring dynamic DAX measures, interactive forecasting tools, and a hybrid Excel + MySQL data pipeline.

---

## 🎯 Business Problem

Hospital administrators typically operate across siloed systems — patient records in one place, billing in another, doctor scheduling elsewhere. This fragmentation leads to:

- Delayed clinical decision-making due to manual reporting
- No single source of truth for financial performance
- Inability to track real-time bed availability or discharge rates
- Limited visibility into individual doctor productivity and commissions

**This dashboard solves all of the above in a single, interactive interface.**

---

## 🗂️ Project Architecture

### Data Sources
| Source | Role |
|--------|------|
| **Microsoft Excel** | Primary flat-file data store for 16 administrative tables |
| **MySQL (via localhost)** | Enterprise-grade relational database for server-side processing |

### Optimized Data Model (16 → 5 Tables)

Through systematic ETL consolidation, the raw schema was compressed from 16 disparate tables into 5 core analytical entities:

```
┌─────────────────────┐
│   Patient Info       │  ◄── Master Table (30 unique patients)
│   (Master)           │
└──────────┬──────────┘
           │ Many-to-One Relationships
     ┌─────┼──────────────────────────┐
     ▼     ▼                          ▼
┌─────────┐ ┌───────────┐ ┌──────────────────┐
│Medicine │ │Appointment│ │ Patient Test      │
└─────────┘ └───────────┘ └──────────────────┘
                 │
                 ▼
        ┌──────────────────┐
        │  Medical Stock   │
        └──────────────────┘
```

---

## 🛠️ Technical Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| **Data Storage** | Excel + MySQL | Dual-source hybrid data environment |
| **ETL & Modeling** | Power Query (M Language) | Schema consolidation, transformations |
| **Analytics** | DAX (Data Analysis Expressions) | KPIs, dynamic measures, conditional logic |
| **Visualization** | Power BI Desktop | Interactive reports and dashboards |
| **Schema Migration** | Python (Custom Uploader) | Batch upload of Excel files into MySQL |

---

## ⚙️ Data Engineering Highlights

### Dynamic File Path Parameter
Rather than embedding static file paths — which cause `Source Not Found` errors when projects are shared — a **File Path Parameter** was implemented in the M-code. This acts as a global variable that updates the root directory across all 16 source queries simultaneously, enabling zero-friction project portability.

### MySQL Integration & Query Folding
Power BI connects to MySQL via `localhost` using the MySQL Connector. This setup enables **query folding** — pushing transformation logic back to the database server — which reduces computational load on the Power BI client and significantly improves refresh performance at scale.

### Schema Consolidation via Power Query Merges
- `Satisfaction Scores`, `Surgery`, and `Bed Status` tables were merged into the `Patient Info` master using **LEFT JOIN** logic in Power Query
- The `Bills` table was **unpivoted** from wide format (static charge columns) to a long attribute-value structure, enabling dynamic categorical bar charts for financial analysis

### SQL DISTINCT Subquery for Join Integrity
To prevent row inflation when joining the `Appointment` and `Doctor` tables — which would have inflated the patient count from 30 to 35 due to multiple appointments per patient — a **DISTINCT subquery** was executed at the database level, producing a clean unique Patient-Doctor bridge table.

---

## 📊 Dashboard Pages

### 1. Overview Dashboard
Consolidated snapshot of hospital-wide KPIs including total patients, discharge rate, revenue, medicine stock levels, bed status, and charge breakdown by category.

**Key Metrics Tracked:**
- 30 Total Patients | 73.3% Discharge Rate
- 854K Total Revenue | 4,030 Medicine Units in Stock
- 16 Available Beds
- Charge breakdown: Surgery (565K) → Room (197K) → Tests → Doctor Fees → Medicine

### 2. Patient Dashboard
Per-patient drill-down view with dynamic selection. Displays clinical status (Admitted/Discharged), admit and discharge dates, diagnosis, charges, medicine purchase history, and patient satisfaction ratings.

**Logic Highlight — Admit Status Classification:**
```DAX
Admit Status = IF(ISBLANK('Patient Info'[Bed ID]), "Discharged", "Admitted")
```

### 3. Doctor Dashboard
Individual doctor performance view featuring appointment history, patient outcomes, fees, commissions, and an **interactive forecasting calculator**.

**Dynamic Commission Calculator:**
Uses Numeric Range Parameters with sliders. As the user adjusts the Commission Rate (10%–100%) and Patient Amount inputs, the `Commission Earned` measure recalculates in real time — functioning as a live financial forecasting tool for clinical staffing decisions.

### 4. Hospital Dashboard
Operational view covering:
- **Bed occupancy by ward type** (General / Private / ICU)
- **Patient age category distribution** (18–30, 31–45, 46–60, 60+)
- **Patient test status log** with clinical notes
- **Doctor appointment tracker** with conditional status icons (Scheduled vs. Completed)

### 5. Finance Dashboard
Full financial intelligence layer including:
- Revenue by payment method (Cash / Credit Card / Insurance)
- Monthly revenue trend (May–Oct 2023)
- Medicine stock vs. sold comparison across 10 drug categories
- Supplier performance (HealthCare Supplies, MediCore Enterprises, MediPharm Distributors)
- Salary and commission breakdown across 15 doctors and 20 staff

---

## 🧮 DAX Measures & Analytical Logic

### Total Bill Calculation
```DAX
Total Bill =
    SUM(Room Charges)
  + SUM(Surgery Charges)
  + SUM(Medicine Charges)
  + SUM(Test Charges)
  + SUM(Doctor Fees)
  - SUM(Discount)
```

### Star Rating System
Patient satisfaction visualized as a 5-star scale using the Power BI Star Rating Quick Measure, anchored to the `Satisfaction Score` field.

### Appointment Status Icon (Conditional Column)
```DAX
Status Icon =
  IF('Appointment'[Status] = "Completed",
     [Check Icon URL],
     [Calendar Icon URL])
```

---

## 🔧 Advanced Implementation Details

| Challenge | Solution |
|-----------|----------|
| Alphabetical month sorting | Applied `Sort by Column` using a `Month Index` field |
| Heatmap readability | Conditional font color (white on dark cells) for high-density medicine tracking |
| Filter state management | Bookmarks with `Data` option disabled to preserve user filter context when toggling slicer panels |
| Null dashboard states | `Forced Selection` enabled on critical slicers to prevent empty visual states |
| Relationship integrity | All relationships directed Many-to-One toward `Patient Info` master table |

---

## 🚀 Getting Started

### Prerequisites
- [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free)
- MySQL Server + [MySQL Connector for Power BI](https://dev.mysql.com/downloads/connector/net/) *(optional — Excel source works standalone)*

### Setup Instructions

**Option A — Excel Only (Recommended for Quick Start)**
1. Clone this repository
2. Open `Hospital_Dashboard.pbix` in Power BI Desktop
3. Go to **Home → Transform Data → Manage Parameters**
4. Update the `File Path` parameter to your local folder path
5. Click **Refresh** — all 16 source queries will update automatically

**Option B — MySQL Backend**
1. Run the Python uploader script to migrate Excel tables into MySQL:
   ```bash
   python excel_uploader.py --host localhost --db hospital_db
   ```
2. Open Power BI and update the MySQL `localhost` connection credentials
3. Refresh the data model

---

## 📈 Key Performance Indicators (Summary)

| KPI | Value |
|-----|-------|
| Total Patients | 30 |
| Discharge Rate | 73.3% |
| Total Revenue | $854K |
| Surgery Revenue | $565K |
| Avg Patient Spend | $6,810 |
| Avg Patient Age | 46 |
| Total Doctors | 15 |
| Total Staff | 20 |
| Doctor Salaries | $2.10M |
| Doctor Commission | $20.44K |
| Medicine Units in Stock | 4,030 |

---

## 🔭 Future Roadmap

The modular 5-table schema and parameterized data pipeline are designed for extensibility. Planned enhancements include:

- **Pharmacy Stock Management** page with reorder threshold alerts
- **Departmental Finance** breakdown (Cardiology, Oncology, Dermatology)
- **Predictive Analytics** layer using Azure ML integration within Power BI
- **Row-Level Security (RLS)** to scope dashboard access by doctor or department
- **Automated refresh** via Power BI Service with scheduled gateway
