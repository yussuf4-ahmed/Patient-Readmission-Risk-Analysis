# Patient-Readmission-Risk-Analysis
## 📌 Overview
This project analyzes patient readmission trends, risk factors, and KPIs using SQL Server, Power BI, and a clean dataset.  
The dashboard provides insights into:
- Total Patients
- Readmitted Patients
- Readmission Rate
- Trends by Month, Age Group, and Gender
- Primary Diagnosis Analysis
  
## 📌 Features
- **Risk scoring model** based on procedures, days in hospital, and comorbidities.
- **Views** for patient readmission risk analysis.
- **Aggregated metrics** for readmission rates by category, age group, discharge destination, and diagnosis.
- **Data manipulation** including date assignments for patient visits.
- **Power BI dashboard** for visualization.


## ⚙️ Tools & Technologies
- **SQL Server** – Data cleaning & transformation
- **Power BI** – Dashboard & visualizations
- **Excel** – Initial data preparation
- **GitHub** – Version control & project hosting

## 📊 Dashboard Preview
![Dashboard](images/dashboard_preview.png)

## 📜 SQL Scripts
### 1️⃣ View Patient Data
```sql
USE PatientData;

SELECT * FROM PatientData;

### 2️⃣ Calculate Risk Score & Risk Category


## 🚀 How to Use
1. Import `PatientData_Clean.csv` into SQL Server
2. Run SQL scripts in the `sql/` folder
3. Open `Patient_Readmission_Risk_Dashboard.pbix` in Power BI
4. Connect to your SQL Server instance
5. Refresh the dashboard






