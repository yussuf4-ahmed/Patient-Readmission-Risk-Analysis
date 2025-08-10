# Patient-Readmission-Risk-Analysis
## 📌 Overview
This project analyzes patient readmission trends, risk factors, and KPIs using SQL Server, Power BI, and a clean dataset.  
The dashboard provides insights into:
- Total Patients
- Readmitted Patients
- Readmission Rate
- Trends by Month, Age Group, and Gender
- Primary Diagnosis Analysis

## ⚙️ Tools & Technologies
- **SQL Server** – Data cleaning & transformation
- **Power BI** – Dashboard & visualizations
- **Excel** – Initial data preparation
- **GitHub** – Version control & project hosting

## 📊 Dashboard Preview
![Dashboard](images/dashboard_preview.png)

## 📜 SQL Scripts
1. `01_create_table.sql` – Table creation & schema definition
` USE PatientData;

SELECT * FROM PatientData;

---- Risk_Score and Risk_Category----

SELECT *,
    (num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3) AS Risk_Score,
    CASE 
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 24 THEN 'High Risk'
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 12 THEN 'Moderate Risk'
        ELSE 'Low Risk'
    END AS Risk_Category
FROM PatientData ;

-- view --

CREATE VIEW vw_patient_readmission_risk AS
SELECT *,
    (num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3) AS Risk_Score,
    CASE 
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 24 THEN 'High Risk'
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 12 THEN 'Moderate Risk'
        ELSE 'Low Risk'
    END AS Risk_Category
FROM PatientData ;


-- Total Patients --
SELECT COUNT(*) AS Total_Patients FROM vw_patient_readmission_risk;

-- Readmitted vs Not --
SELECT 
    CASE WHEN readmitted = 1 THEN 'Yes' ELSE 'No' END AS Readmitted_Status,
    COUNT(*) AS Total
FROM vw_patient_readmission_risk
GROUP BY 
    CASE WHEN readmitted = 1 THEN 'Yes' ELSE 'No' END;

-- Readmission Rate by Risk Category --
SELECT 
    risk_category,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY risk_category;


-- Readmission by Age Groups --
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

-- Readmissions by Discharge Destination --
SELECT 
    discharge_to,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY discharge_to;

--  Readmission by Diagnosis --
SELECT 
    primary_diagnosis,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY primary_diagnosis;

-- Add VisitDate column
ALTER TABLE PatientData
ADD VisitDate DATE;

-- Update it with random dates in 2024
UPDATE PatientData
SET VisitDate = DATEADD(DAY, ABS(CHECKSUM(NEWID())) % 365, '2024-01-01'); `

## 🚀 How to Use
1. Import `PatientData_Clean.csv` into SQL Server
2. Run SQL scripts in the `sql/` folder
3. Open `Patient_Readmission_Risk_Dashboard.pbix` in Power BI
4. Connect to your SQL Server instance
5. Refresh the dashboard






