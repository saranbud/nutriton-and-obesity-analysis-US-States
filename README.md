# 🥗 Nutrition & Obesity Analysis — US States

## 📌 Project Overview

This project analyzes the **CDC Behavioral Risk Factor Surveillance System (BRFSS)** dataset to explore patterns in nutrition, physical activity, and obesity across US states. The pipeline covers data exploration, cleaning, and a series of targeted analytical questions spanning demographics, socioeconomic factors, and health behaviors.

---

## 📂 Data Source

| Detail | Value |
|---|---|
| **Catalog** | `nutrition_and_obesity_data` |
| **Schema** | `default` |
| **Table** | `nutrition_physical_activity_and_obesity_behavioral_risk_factor_surveillance_system` |
| **Total Columns** | 33 |
| **Coverage** | US States, multiple years |
| **Topics** | Obesity / Weight Status, Physical Activity, Fruits & Vegetables |

---

## 🔁 Pipeline Architecture

The pipeline is organized into three phases:

```
Phase 1: Exploratory Analysis (branching from raw source)
Phase 2: Data Cleaning (drop columns → filter nulls)
Phase 3: Analytical Queries (branching from clean dataset)
```

---

## 🔍 Phase 1 — Exploratory Analysis

These steps run directly on the **raw source** and help understand the data before cleaning.

### 1. `dataset_shape`
Returns the total number of rows and confirms the table has **33 columns**.

---

### 2. `null_analysis`
Computes the **null count and null percentage** for every column in the dataset.
Ordered by highest null percentage first — helps prioritize what needs cleaning.

---

### 3. `full_null_analysis`
An extended version of `null_analysis` that also computes the **filled percentage** (non-null rows).
Returns both `null_percentage` and `filled_percentage` side by side for all 33 columns.

---

### 4. `footnote_check`
Investigates **why Data_Value is NULL** by grouping rows on `Data_Value_Footnote`.
Reveals whether nulls are due to suppressed data, small sample sizes, or missing values.

---

### 5. `location_id_check`
Counts **distinct LocationID, LocationDesc, and LocationAbbr** values.
Confirms that each location ID maps to one unique state name and abbreviation.

---

### 6. `stratification_values`
Lists every `StratificationCategory1` group (e.g. Total, Sex, Age, Income, Education, Race/Ethnicity)
and shows all distinct `Stratification1` values within each group.

---

## 🧹 Phase 2 — Data Cleaning

### 7. `drop_useless_columns`
Drops **13 redundant or ID-only columns** that add no analytical value:

| Dropped Column | Reason |
|---|---|
| `Data_Value_Unit` | Always `%`, no variation |
| `Total` | Always null or redundant |
| `Data_Value_Footnote_Symbol` | Only relevant alongside footnote text |
| `Data_Value_Footnote` | Explanatory text, not numeric |
| `Data_Value_Alt` | Duplicate of Data_Value |
| `LocationID` | Replaced by LocationDesc |
| `LocationAbbr` | Replaced by LocationDesc |
| `ClassID` | ID version of Class (text is sufficient) |
| `TopicID` | ID version of Topic |
| `QuestionID` | ID version of Question |
| `DataValueTypeID` | ID version of Data_Value_Type |
| `StratificationCategoryId1` | ID version of StratificationCategory1 |
| `StratificationID1` | ID version of Stratification1 |

---

### 8. `clean_base_dataset` (Filter)
Filters down to **analysis-ready rows** by removing:
- Rows where `Data_Value IS NULL` (no measurable value)
- Rows where `Stratification1 = 'Data not reported'` (suppressed/unavailable data)

> ⚠️ All downstream analytical queries use **this cleaned dataset** as their input.

---

## 📊 Phase 3 — Analytical Questions

All queries below use `clean_base_dataset` as the base.

---

### Q1 — State Obesity Trends Over Time
Tracks **average obesity rate per state per year**, alongside the **national average** and the difference (`vs_national_avg`).
Sorted by most recent year first, then highest obesity rate.

---

### Q1a — Obesity by Sex per State
Breaks down obesity rates by **Male / Female** for each state.
Includes each state's overall rate and deviation from the national average.

---

### Q1b — Obesity by Age Group per State
Breaks down obesity rates by **age brackets** (18–24, 25–34, ..., 65+) for each state.

---

### Q1c — Obesity by Education Level per State
Breaks down obesity rates by **education level** (Less than high school → College graduate) per state.

---

### Q1d — Obesity by Income Bracket per State
Breaks down obesity rates by **income bracket** (< $15k → $75k+) per state.

---

### Q1e — Obesity by Race/Ethnicity per State
Breaks down obesity rates by **race/ethnicity group** per state.

---

### Q2 — Obesity by Income Level (National)
Aggregates obesity rates **nationally by income bracket**.
Shows which income groups have the highest obesity burden vs the national average.

---

### Q3 — Physical Activity by Sex (National)
Compares **physical inactivity rates between Male and Female** nationally.
Reveals whether one sex is more at risk for inactivity-related health issues.

---

### Q4 — Nutrition Risk by Age Group (National)
Analyzes **fruit & vegetable consumption patterns across age groups**.
Lower rates indicate higher nutritional risk.

---

### Q5 — Education vs Obesity & Physical Inactivity
Compares **obesity rate AND physical inactivity rate** across education levels.
Joins both classes together to show how education correlates with two key health outcomes.

---

### Q6 — Racial & Ethnic Health Disparities
Compares **obesity rate AND physical inactivity rate** across racial/ethnic groups.
Highlights which communities face the greatest health disparities relative to the national average.

---

### Correlation Analysis — Factors vs Obesity
Computes **Pearson correlation coefficients** between state-level obesity rates and five key factors:

| Factor | Method |
|---|---|
| Physical Inactivity | Direct avg rate per state-year |
| Poor Nutrition (Low Fruit/Veg) | Direct avg rate per state-year |
| Lower Income | Weighted income index (bracket encoded 1–6, lower = lower income) |
| Lower Education | Weighted education index (bracket encoded 1–4, lower = lower education) |
| Older Age | Weighted age index (midpoint of each bracket: 21, 30, 40, 50, 60, 70) |

Results are labeled **Strong** (|r| ≥ 0.7), **Moderate** (|r| ≥ 0.4), or **Weak**.

---

## 🛠️ Tech Stack

- **Platform:** Databricks Lakeflow Designer
- **Storage:** Unity Catalog (Delta Lake)
- **Query Language:** Spark SQL
- **Data:** CDC BRFSS Public Dataset

---

## 👤 Author

**Saranya** — [@saranbud](https://github.com/saranbud)
