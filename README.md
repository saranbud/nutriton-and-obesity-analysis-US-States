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


---

## 📊 Dashboard — The American Obesity Crisis: A State-by-State Analysis (2011–2024)

Built with **Databricks Lakeflow Designer** | Source: CDC BRFSS | 110,000+ rows · 55 states/territories · 14 years (2011–2024)

---

### Panel 1 — National Overview

**2024 National Obesity Rate: 34.2%**

The opening panel sets the stage with two key visuals: a KPI counter showing the current national obesity rate, and a line chart tracking the national obesity trend from 2011 to 2024. The trend shows a steady, consistent rise from ~32% in 2011 to 34.2% in 2024 — confirming that obesity in America has been on an unbroken upward trajectory for over a decade.

> 📸 *Screenshot placeholder — add image here*
> 
> `![Panel 1 - National Overview](screenshots/panel1_overview.png)`

---

### Panel 2 — U.S. Obesity Rate by State (2024)

**Geographic breakdown + state rankings**

The left side shows a **choropleth map** of the US with states shaded by obesity rate — darker red = higher obesity. The right side shows a ranked bar chart of all 55 states/territories with the national average (34.15%) marked as a reference line. West Virginia ranks highest; Hawaii ranks lowest. Midwest and Southern states cluster above the national average, while coastal states trend below it.

> 📸 *Screenshot placeholder — add image here*
>
> `![Panel 2 - State Map](screenshots/panel2_state_map.png)`

---

### Panel 3 — Who Is Most At Risk?

**Obesity rates broken down by sex and age group. Red line = national average (~33%).**

Two bar charts reveal which demographic groups bear the highest burden:
- **By Sex:** Males (~35%) have a significantly higher obesity rate than females (~30.5%), both measured against the 33.0% national average.
- **By Age Group:** Middle-aged adults (45–54 and 55–64) have the highest rates (~37%), while the youngest group (18–24) has the lowest (~26%). Obesity risk rises sharply through middle age before declining slightly in the 65+ group.

> 📸 *Screenshot placeholder — add image here*
>
> `![Panel 3 - Demographics](screenshots/panel3_demographics.png)`

---

### Panel 4 — Socioeconomic Factors

**Obesity rates by education level and household income. Higher socioeconomic status is associated with lower obesity rates.**

Two bar charts highlight the socioeconomic gradient of obesity:
- **By Education:** College graduates have the lowest obesity rate (~31%), noticeably below the national average. All other groups (Less than high school, Some college, High school graduate) cluster near or above the national average at ~33–34%.
- **By Income:** The pattern is less dramatic but still present — the $75,000+ income group shows the lowest obesity rate, while middle-income brackets ($35,000–$74,999) show the highest. Lower economic access to healthy food and exercise likely drives this relationship.

> 📸 *Screenshot placeholder — add image here*
>
> `![Panel 4 - Socioeconomic Factors](screenshots/panel4_socioeconomic.png)`

---

### Panel 5 — Racial & Ethnic Disparities

**Average obesity rates by race/ethnicity across all states.**

A horizontal bar chart reveals stark disparities across racial and ethnic groups:
- **Non-Hispanic Black** (~35.5%) and **NH Hawaiian/Pacific Islander** (~35%) face the highest obesity burden — both significantly above the national average.
- **NH American Indian/Alaska Native** and **Hispanic** populations also exceed the national average.
- **Non-Hispanic White** and **Non-Hispanic Other** sit near the national average.
- **Non-Hispanic Asian** has the lowest rate by a wide margin (~27%), well below all other groups.

These disparities reflect systemic inequities in access to healthcare, food environments, and economic opportunities across communities.

> 📸 *Screenshot placeholder — add image here*
>
> `![Panel 5 - Racial & Ethnic Disparities](screenshots/panel5_racial.png)`

---

> 💡 **To add screenshots:** Upload your images to a `screenshots/` folder in this repo and replace the placeholder paths above with the actual filenames.
