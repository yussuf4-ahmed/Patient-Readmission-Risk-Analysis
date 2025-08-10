# 🏥 Patient Readmission Risk Analysis

## 📌 Overview  
This project analyzes **patient readmission trends, risk factors, and KPIs** using **SQL Server**, **Power BI**, and a clean dataset.  

The dashboard provides insights into:  
- 📈 **Total Patients**  
- 🔄 **Readmitted Patients**  
- 📊 **Readmission Rate**  
- 📅 **Trends by Month, Age Group, and Gender**  
- 🩺 **Primary Diagnosis Analysis**  

---

## ✨ Features  
- **Risk scoring model** based on procedures, days in hospital, and comorbidities.  
- **Views** for patient readmission risk analysis.  
- **Aggregated metrics** for readmission rates by category, age group, discharge destination, and diagnosis.  
- **Data manipulation** including date assignments for patient visits.  
- **Interactive Power BI dashboard** for visualization.  

---

## ⚙️ Tools & Technologies  
- **SQL Server** – Data cleaning & transformation  
- **Power BI** – Dashboard & visualizations  
- **Excel** – Initial data preparation  
- **GitHub** – Version control & project hosting  

--- 

---

## 📜 SQL Scripts  

### 1️⃣ Create Risk Score & Risk Category
```sql
USE PatientData;

SELECT *,
    (num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3) AS Risk_Score,
    CASE 
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 24 THEN 'High Risk'
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 12 THEN 'Moderate Risk'
        ELSE 'Low Risk'
    END AS Risk_Category
FROM PatientData;
```

---

### 2️⃣ Create View for Analysis
```sql
CREATE VIEW vw_patient_readmission_risk AS
SELECT *,
    (num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3) AS Risk_Score,
    CASE 
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 24 THEN 'High Risk'
        WHEN ((num_procedures * 2) + (days_in_hospital * 1.5) + (comorbidity_score * 3)) > 12 THEN 'Moderate Risk'
        ELSE 'Low Risk'
    END AS Risk_Category
FROM PatientData;
```

---

### 3️⃣ KPIs & Metrics Queries  

**Total Patients**  
```sql
SELECT COUNT(*) AS Total_Patients 
FROM vw_patient_readmission_risk;
```

**Readmitted vs Not**  
```sql
SELECT 
    CASE WHEN readmitted = 1 THEN 'Yes' ELSE 'No' END AS Readmitted_Status,
    COUNT(*) AS Total
FROM vw_patient_readmission_risk
GROUP BY CASE WHEN readmitted = 1 THEN 'Yes' ELSE 'No' END;
```

**Readmission Rate by Risk Category**  
```sql
SELECT 
    risk_category,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY risk_category;
```

**Readmission by Age Group**  
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
```

**Readmissions by Discharge Destination**  
```sql
SELECT 
    discharge_to,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY discharge_to;
```

**Readmissions by Diagnosis**  
```sql
SELECT 
    primary_diagnosis,
    COUNT(*) AS Total_Patients,
    SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) AS Readmitted_Patients,
    ROUND(100.0 * SUM(CASE WHEN readmitted = 1 THEN 1 ELSE 0 END) / COUNT(*), 2) AS Readmission_Rate
FROM vw_patient_readmission_risk
GROUP BY primary_diagnosis;
```

---

### 4️⃣ Add Visit Date for Trends  
```sql
ALTER TABLE PatientData
ADD VisitDate DATE;

UPDATE PatientData
SET VisitDate = DATEADD(DAY, ABS(CHECKSUM(NEWID())) % 365, '2024-01-01');
```

---

## 📊 Power BI Dashboard Components  

**KPIs**  
- Total Patients  
- Readmitted %  
- High-Risk Patients  

**Charts**  
1. 📅 Readmission Trend by Month  
2. 📊 Readmission Rate by Risk Category  
3. 👥 Readmission by Age Group  
4. 🏥 Readmission by Discharge Destination  
5. 🩺 Readmission by Diagnosis  
6. 🌍 Geographic Map *(if location data available)*  

---

## 🚀 How to Run  

1. Load the dataset into **SQL Server**.  
2. Run the **SQL scripts** in the order above.  
3. Connect **Power BI** to SQL Server.  
4. Import **vw_patient_readmission_risk** view into Power BI.  
5. Build the visuals as described in the dashboard components section.  
