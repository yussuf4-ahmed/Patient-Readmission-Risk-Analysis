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
``sql
USE PatientData;

SELECT * FROM PatientData;

### 2️⃣ Calculate Risk Score & Risk Category
```sql
SELECT *,
    (num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3) AS Risk_Score,
    CASE 
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 24 THEN 'High Risk'
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 12 THEN 'Moderate Risk'
        ELSE 'Low Risk'
    END AS Risk_Category
FROM PatientData;
### 3️⃣ Create Readmission Risk View
``sql
CREATE VIEW vw_patient_readmission_risk AS
SELECT *,
    (num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3) AS Risk_Score,
    CASE 
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 24 THEN 'High Risk'
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 12 THEN 'Moderate Risk'
        ELSE 'Low Risk'
    END AS Risk_Category
FROM PatientData;

### 4️⃣ KPI Queries
#### Total Patients
```sql
SELECT COUNT(*) AS Total_Patients 
FROM vw_patient_readmission_risk;

#### Readmitted vs Not
```sql
SELECT 
    CASE WHEN readmitted = 1 THEN 'Yes' ELSE 'No' END AS Readmitted_Status,
    COUNT(*) AS Total
FROM vw_patient_readmission_risk
GROUP BY 
    CASE WHEN readmitted = 1 THEN 'Yes' ELSE 'No' END;

#### Readmission Rate by Risk Category
```sql
SELECT 
    risk_category,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY risk_category;

#### Readmission by Age Groups
```sql
SELECT 
    CASE 
        WHEN age BETWEEN 0 AND 18 THEN '0-18'
        WHEN age BETWEEN 19 AND 35 THEN '19-35'
        WHEN age BETWEEN 36 AND 50 THEN '36-50'
        WHEN age BETWEEN 51 AND 65 THEN '51-65'
        ELSE '66+'
    END AS Age_Group,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY 
    CASE 
        WHEN age BETWEEN 0 AND 18 THEN '0-18'
        WHEN age BETWEEN 19 AND 35 THEN '19-35'
        WHEN age BETWEEN 36 AND 50 THEN '36-50'
        WHEN age BETWEEN 51 AND 65 THEN '51-65'
        ELSE '66+'
    END;
#### Readmissions by Discharge Destination
```sql
SELECT 
    discharge_to,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY discharge_to;

#### Readmissions by Diagnosis
```sql
SELECT 
    primary_diagnosis,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY primary_diagnosis;

### 5️⃣ Add Visit Date for Trends
```sql
ALTER TABLE PatientData
ADD VisitDate DATE;

UPDATE PatientData
SET VisitDate = DATEADD(DAY, ABS(CHECKSUM(NEWID())) % 365, '2024-01-01');

### 📊 Power BI Dashboard
**KPIs**: Total Patients, Readmitted %, High-Risk Patients.
**Charts**:
Readmission Trend by Month
Readmission Rate by Risk Category
Readmission by Age Group
Readmission by Discharge Destination
Readmission by Diagnosis
Geographic Map (if location data available)

### 🚀 How to Run
Load the dataset into SQL Server.
Run the SQL scripts in order.
Connect Power BI to SQL Server.
Import the ``` sql view vw_patient_readmission_risk and build visuals












