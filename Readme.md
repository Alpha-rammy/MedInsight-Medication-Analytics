# 💊 MedInsight — Medication Utilisation & Cost Analytics

### Exploring Patient Medication Utilisation, Expenditure Variation and Cost Trends Using Power BI

Images/executive_overview.png

**Tools Used:** Power BI • DAX • Power Query • Data Modelling • Healthcare Analytics

---

## 📖 Project Overview

Medication utilisation and expenditure are important considerations in healthcare resource planning, medicines optimisation, and population health management.

In this project, I analysed synthetic Synthea healthcare data to understand how recorded medication activity and expenditure vary across patient groups and over time.

Rather than simply creating a dashboard, I wanted to answer a practical stakeholder question:

**How does medication utilisation and expenditure vary across patient groups over time, and which patterns warrant further investigation?**

A key consideration throughout this analysis was recognising that higher medication utilisation does not automatically indicate inappropriate prescribing or inefficient healthcare spending.

Differences in utilisation may reflect:

- Chronic disease burden
- Treatment complexity
- Multiple concurrent conditions
- Long-term therapies
- Age-related healthcare needs

I therefore approached this project as a decision-support exercise, using analytics to identify patterns that may warrant further investigation rather than drawing conclusions about treatment appropriateness.

---

## 🎯 Stakeholders

I designed this dashboard primarily for:

- Medicines Optimisation Teams
- Pharmacy & Medication Management Teams
- Healthcare Finance Teams
- Resource Planning Teams

The solution could also support:

- Healthcare Data Analysts
- Population Health Analysts
- Clinical Governance Teams
- Service Managers

---

## ❓ Business Questions

Before building the dashboard, I translated the overall problem into five business questions:

1. What is the overall scale of recorded medication activity and expenditure?
2. How does medication utilisation vary across patient groups?
3. Which patient segments account for the greatest medication expenditure?
4. How does medication expenditure change over time?
5. Which utilisation or expenditure patterns warrant further investigation?

These questions guided my data preparation, modelling, DAX development, and dashboard design.

---

## 🗂️ Dataset Overview

For this project, I used synthetic healthcare data generated with Synthea.

| Table | Records | Purpose |
|---|---|---|
| DimPatient | 1,163 | Patient demographics and segmentation |
| FactMedication | 56,430 | Medication episodes and expenditure |
| DimMedication | 170 | Medication descriptions |
| FactConditions | Various | Condition history |
| DimDate | Calendar Table | Time-based analysis |

---

## 🔍 Understanding the Data Grain

One of the first questions I asked was:

**What does one row represent?**

### FactMedication

One row represents:

**One recorded medication episode**

This means a patient may appear multiple times because they can have multiple medication episodes.

### DimPatient

One row represents:

**One unique patient**

Understanding this distinction was important because counting medication records does not provide the number of unique patients.

For population measures, I therefore used `DISTINCTCOUNT()` rather than counting rows.

---

## ✅ Data Quality Assessment

Before analysing the data, I performed several validation checks, including:

- Missing patient identifiers
- Missing medication descriptions
- Invalid dates
- Duplicate patient records
- Duplicate medication episodes
- Relationship key consistency
- Data type validation
- Missing medication stop dates

### Handling Missing Stop Dates

I deliberately retained records with missing medication stop dates because a missing stop date does not necessarily indicate invalid data.

It may simply represent:

- Ongoing medication
- Missing administrative data
- An episode without a recorded end date

---

## 🛠️ Data Preparation

Using Power Query, I completed the following transformation steps:

- Imported source datasets
- Assigned appropriate data types
- Validated identifiers
- Prepared medication start and stop dates
- Created medication duration fields
- Created medication status fields
- Calculated patient age
- Built patient age groups
- Prepared fact and dimension tables
- Validated data model relationships

---

## ⭐ Star Schema Data Model

To improve performance and simplify analysis, I organised the solution using a star-schema design.

### Data Model

Images/star_schema.png

```text
          DimDate
             |
             v
DimPatient ---> FactMedication <--- DimMedication
                     |
                     v
                FactConditions
```

### Why I Used a Star Schema

Separating medication activity, patient information, medication information, and calendar attributes provided several benefits:

- ✅ Improved model performance
- ✅ Reduced duplication
- ✅ Simplified DAX measures
- ✅ Consistent filtering behaviour
- ✅ Easier report maintenance

---

## 📏 DAX Measures

To answer the business questions, I created several DAX measures.

### Total Patients

```dax
Total Patients = DISTINCTCOUNT(DimPatient[Id])
```

### Total Medication Episodes

```dax
Total Medication Episodes = COUNTROWS(FactMedication)
```

### Total Medication Cost

```dax
Total Medication Cost = SUM(FactMedication[TOTALCOST])
```

### Average Cost Per Patient

```dax
Average Cost per Patient =
DIVIDE(
    [Total Medication Cost],
    [Total Patients],
    0
)
```

I also implemented:

- Month-on-Month Cost Change
- Year-on-Year Cost Change
- Rolling 3-Month Medication Cost
- Previous Month Cost
- Previous Year Cost

to analyse expenditure trends over time.

---

## 📊 Dashboard 1 — Executive Overview

Images/executive_overview.png

I designed the Executive Overview page to provide stakeholders with a high-level summary of medication activity and expenditure.

### Key Metrics

| Metric | Value |
|---|---|
| Total Patients | 1,163 |
| Patients with Medication Activity | 1,117 |
| Medication Episodes | 56,430 |
| Total Medication Expenditure | $73.91M |

### Key Findings

- Approximately 96% of patients had recorded medication activity.
- Total recorded medication expenditure exceeded $73.91 million.
- 93.5% of medication episodes were classified as stopped.
- 6.5% were classified as ongoing.

The page combines expenditure trends, medication status, demographics, and top-cost medications to provide a quick understanding of overall performance before users drill deeper into the analysis.

---         

## 👥 Dashboard 2 — Patient Segmentation

Images/patient_segmentation.png

After understanding overall activity, I wanted to determine whether medication utilisation was distributed evenly across the patient population.

Patients were grouped according to medication episode volume.

### Segment Distribution

| Segment | Patients |
|---|---|
| High Utilisation | 511 |
| Medium Utilisation | 223 |
| Low Utilisation | 383 |
| No Recorded Medication | 46 |

### Average Recorded Cost Per Patient

| Segment | Average Cost |
|---|---|
| High Utilisation | ~$20,000 |
| Medium Utilisation | ~$5,300 |
| Low Utilisation | ~$1,100 |

### Key Insight

The High Utilisation segment accounted for the largest proportion of recorded medication expenditure.

However, I was careful not to interpret this as evidence of inappropriate prescribing because higher utilisation may reflect:

- Chronic disease burden
- Treatment complexity
- Age-related healthcare needs
- Long-term therapies
- Multiple concurrent conditions

Instead, I viewed these patients as a cohort that may warrant additional investigation when combined with clinical context.

---

## 📈 Dashboard 3 — Medication Trends & Cost

Images/medication_trends_cost.png

To understand how expenditure changed over time, I developed measures and visualisations for:

- Monthly Medication Cost
- Annual Medication Cost
- Rolling 3-Month Cost
- Medication Activity Trends
- Month-on-Month Change
- Year-on-Year Change

### Key Insight

The analysis revealed several periods of elevated expenditure throughout the reporting period.

The dashboard successfully identifies when expenditure changed, but it does not establish why expenditure changed.

Potential explanations include:

- Changes in medication volume
- Changes in medication mix
- Treatment duration differences
- Population characteristics
- Synthetic data generation effects

Further investigation would be required before attributing changes to specific clinical or operational factors.

---

## 🔑 Key Findings

### 1. Significant Medication Activity

The dataset contained:

- 56,430 medication episodes
- $73.91M in recorded expenditure

### 2. Most Patients Had Medication Activity

Of the 1,163 patients included in the dataset, 1,117 had at least one recorded medication episode.

### 3. High Utilisation Was Common

The High Utilisation segment contained 511 patients, representing 43.94% of the population.

### 4. Costs Varied Significantly Between Segments

Average recorded medication costs increased substantially across utilisation groups, with High Utilisation patients accounting for the highest expenditure.

### 5. Expenditure Varied Over Time

Monthly, annual, and rolling analysis revealed notable expenditure fluctuations that may warrant further investigation.

---

## 💡 Recommendations

Based on my findings, I would recommend:

### 1. Review High-Utilisation Cohorts

Prioritise high-utilisation patient groups for further review while considering underlying clinical complexity.

### 2. Investigate High-Cost Medications

Examine whether expenditure is driven by:

- Cost per medication
- Episode volume
- Treatment duration
- Clinical complexity

### 3. Monitor Medication Cost Trends

Use monthly, annual, and rolling analyses together to identify unusual expenditure patterns.

### 4. Introduce Clinical Context

Future segmentation should incorporate:

- Condition burden
- Comorbidity profiles
- Treatment complexity

### 5. Improve Adherence Measurement

Implement validated adherence metrics such as:

- Proportion of Days Covered (PDC)
- Medication Possession Ratio (MPR)

where suitable data is available.

---

## 🔒 Data Governance & Privacy Considerations

This project uses synthetic Synthea healthcare data, meaning no real patient information was processed.

If I were delivering this analysis using real patient data within an NHS or HIPAA-regulated environment, I would implement additional governance, privacy, and security controls.

These would include:

### Data Minimisation

Accessing only the data required to answer specific business questions.

### De-identification & Pseudonymisation

Removing or pseudonymising direct patient identifiers before analysis.

### Role-Based Access Controls

Restricting access according to job role and operational need.

### Secure Analytics Environments

Conducting analysis within approved and monitored environments with encryption, logging, and audit controls.

### Information Governance Compliance

Ensuring compliance with relevant regulations and frameworks such as:

- UK GDPR
- Data Protection Act 2018
- NHS Data Security & Protection Toolkit
- Caldicott Principles
- HIPAA (where applicable)

### Reporting Controls

Applying aggregation, suppression, and disclosure controls to minimise re-identification risks when reporting small patient cohorts.

This is particularly important when analysis involves sensitive health information.

---

## ⚠️ Limitations

### Synthetic Data

This project uses synthetic Synthea healthcare data and does not represent the performance of any real healthcare organisation.

### Descriptive Rather Than Causal Analysis

The dashboard identifies patterns and associations but does not establish causality.

### Limited Clinical Adjustment

The analysis does not fully adjust for:

- Disease severity
- Comorbidities
- Treatment complexity

### Cost Interpretation

Recorded medication costs should not be interpreted as real-world savings opportunities without additional clinical and financial validation.

---

## 🚀 Future Enhancements

Future versions of MedInsight could incorporate:

- Condition burden analysis
- Comorbidity scoring
- Medication class categorisation
- Cost per treatment day
- Medication adherence analytics
- Predictive expenditure forecasting
- Clinical risk stratification
- Enhanced population health segmentation

---

## 📌 Key Takeaway

Through this project, I demonstrated how Power BI, DAX, data modelling, and healthcare analytics can be combined to explore medication utilisation patterns, identify expenditure trends, and support evidence-based decision-making.

The most important lesson from this analysis is that high utilisation and high expenditure should be viewed as signals for further investigation rather than evidence of inappropriate care.

---

## 🧰 Tools Used

- Power BI
- DAX
- Power Query
- Data Modelling
- Synthea Synthetic Healthcare Data
- Healthcare Analytics
- Time Intelligence

# MedInsight-Medication-Analytics