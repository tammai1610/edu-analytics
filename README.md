# OULAD Student Analytics Project - Technical Documentation

## Project Overview

### Purpose
This project transforms **10+ million clickstream events** from the Open University Learning Analytics Dataset (OULAD) into actionable insights using **Spark SQL**. Working with real data from **32,593 students across 22 courses**, I built a complete analytics pipeline that reveals the hidden patterns between **student engagement, demographics, and academic outcomes**.

### Key Objectives
- Analyze student learning patterns and engagement behaviors
- Identify predictors of academic success and failure
- Create comprehensive views for educational insights
- Build a scalable data pipeline for learning analytics

---

## Project Environment

### Technologies & Tools
- **Database Platform**: Databricks SQL Warehouse
- **Primary Language**: SQL (Spark SQL)
- **Data Processing**: Apache Spark
- **Workspace**: `eduanalytics` database
- **Version Control**: Git-based workflow
- **Analysis Framework**: 12 strategic queries and dashboard preparation

### Infrastructure
- **Cloud Platform**: Databricks
- **Processing Engine**: Apache Spark 3.x
- **Storage**: Delta Lake format
- **Compute**: Serverless SQL warehouse
- **Performance**: Optimized with caching and partitioning

---

## Data Sources & Data Gathering

### Dataset Overview
The **Open University Learning Analytics Dataset (OULAD)** contains comprehensive student interaction data:

- **Source**: Open University (UK) online learning platform
- **Timeframe**: Multiple academic presentations (2013-2014)
- **Scale**: 32,593 students, 22 courses, 10+ million interactions
- **Format**: CSV files imported via Databricks file upload interface

### Core Data Tables
1. **student_info** - Student demographics and enrollment details
2. **student_registration** - Course registration and unregistration dates
3. **student_assessments** - Assessment submissions and scores
4. **student_vle** - Virtual Learning Environment interactions
5. **assessments** - Assessment metadata and types
6. **vle** - VLE resource information
7. **courses** - Course details and presentation information

### Data Ingestion Process
```sql
-- Database workspace creation
CREATE DATABASE eduanalytics;

-- CSV file upload via Databricks interface
-- Tables created: assessments, student_info, student_registration, 
-- student_vle, student_assessments, vle, courses
```

---

## Project Phases & Implementation

### Phase 1: [Data Loading & Table Creation](https://github.com/tammai1610/edu-analytics/blob/main/01-create-table.ipynb)
**Objective**: Load OULAD CSV files into Databricks as SQL tables within the `eduanalytics` workspace

#### Sample Data Structure:
```sql
-- Student Info Table Structure
SELECT * FROM workspace.eduanalytics.student_info LIMIT 5;
-- Fields: code_module, code_presentation, id_student, gender, region, 
-- highest_education, imd_band, age_band, num_of_prev_attempts, 
-- studied_credits, disability, final_result
```

### Phase 2: [Exploratory Data Analysis (EDA) & Data Cleaning](https://github.com/tammai1610/edu-analytics/blob/main/02-create-views.ipynb)
**Objective**: Transform raw OULAD data into clean, analysis-ready tables and create comprehensive views

#### Data Quality Issues Identified:
- **Date Format Problems**: Raw date columns stored as integers (days since course start)
- **Non-numeric Values**: Score and click columns contained non-numeric data requiring casting
- **Missing Value Handling**: Inconsistent NULL handling across related tables

#### Data Quality Assessment Results:
| Table | Total Records | Valid Dates | Null Dates |
|-------|---------------|-------------|------------|
| Assessment Dates | 206 | 195 | 11 |
| Registration Dates | 32,593 | 32,548 | 45 |
| VLE Dates | 10,655,280 | 10,655,280 | 0 |

#### Data Cleaning Solutions:
1. **Date Standardization**:
   - Base Date: Established 2013-02-01 as reference point
   - Conversion Logic: Used `DATE_ADD('2013-02-01', TRY_CAST(date AS INT))` function
   - Tables Cleaned: `assessments_clean`, `studentregistration_clean`, `studentvle_clean`

2. **Data Type Corrections**:
   - Score casting: `TRY_CAST(score AS DOUBLE)` with NULL handling
   - Click casting: `TRY_CAST(sum_click AS INT)` for interaction data

3. **Missing Value Strategy**:
   - Preserved NULL values where meaningful (e.g., unregistration dates)
   - Applied consistent NULL handling across related tables

#### Clean Table Creation:
```sql
-- Example: Assessment table cleaning
CREATE OR REPLACE TABLE eduanalytics.assessments_clean AS
SELECT
    code_module,
    code_presentation,
    id_assessment,
    assessment_type,
    CASE 
        WHEN TRY_CAST(date AS INT) IS NOT NULL
        THEN DATE_ADD('2013-02-01', TRY_CAST(date AS INT))
        ELSE NULL
    END AS date,
    weight
FROM eduanalytics.assessments;
```

### Phase 3: [Comprehensive View Creation](https://github.com/tammai1610/edu-analytics/blob/main/02-create-views.ipynb)
**Objective**: Build analytical views that aggregate student engagement, performance, and demographic data

#### Master Views Created:

1. **v_student_info_complete** - Enhanced student demographics with registration details
2. **v_vle_engagement_summary** - VLE interaction metrics per student
3. **v_assessment_performance** - Assessment scores aggregated by type (TMA, CMA, Exam)
4. **v_student_analytics_master** - Master view combining all student data with derived metrics

#### Key Analytical Features:
- **Engagement Metrics**: Total clicks, unique resources accessed, engagement span
- **Performance Metrics**: Pass rates, average scores by assessment type
- **Demographic Analysis**: Regional, educational, and socioeconomic breakdowns
- **Derived Insights**: Engagement levels, performance predictors

### Phase 4: [Analysis & Insights](https://github.com/tammai1610/edu-analytics/blob/main/03-analysis-dashboard.ipynb)
**Objective**: Execute comprehensive data-driven analysis using SQL to uncover actionable insights

#### Analysis Framework:
The analysis employs **12 strategic queries** addressing critical educational questions:
- What drives student success vs. failure?
- Can we predict at-risk students early?
- How do demographics impact learning outcomes?
- Which engagement patterns lead to better performance?

#### Key Findings:

##### Overall Student Performance Distribution:
- **Pass Rate**: 37.93% (12,361 students) - largest single category
- **Withdrawal Rate**: 31.16% (10,156 students) - significant attrition
- **Failure Rate**: 21.64% (7,052 students)
- **Distinction Rate**: Only 9.28% (3,024 students) - suggesting high academic standards

##### Critical Insight:
> "Only 1 in 3 students crosses the finish line successfully. Nearly a third simply disappear—withdrawing before they fail."

This data reveals a concerning pattern where **62.8% of students** either fail or withdraw, indicating significant challenges in online learning retention and success.

---

## Data Dictionary & Schema Documentation

### Table Relationships (ERD Overview)

```
student_info (1) ────────────── (M) student_registration
     │                              │
     │                              │
     └─────────────┬─────────────────┴─────────────┐
                   │                               │
                   ▼                               ▼
           student_assessments (M)          student_vle (M)
                   │                               │
                   │                               │
                   ▼                               ▼
             assessments (1)                    vle (1)
                   │                               │
                   └─────────────┬─────────────────┘
                                 │
                                 ▼
                            courses (1)
```

### Core Table Schemas

#### student_info
| Column | Type | Description |
|--------|------|-------------|
| code_module | STRING | Course module code (e.g., 'AAA', 'BBB') |
| code_presentation | STRING | Course presentation year/semester |
| id_student | INTEGER | Unique student identifier |
| gender | STRING | Student gender (M/F) |
| region | STRING | Student geographic region |
| highest_education | STRING | Highest educational qualification |
| imd_band | STRING | Index of Multiple Deprivation band |
| age_band | STRING | Age group (0-35, 35-55, 55<=) |
| num_of_prev_attempts | INTEGER | Number of previous course attempts |
| studied_credits | INTEGER | Number of credits studied |
| disability | STRING | Disability status (Y/N) |
| final_result | STRING | Course outcome (Pass/Fail/Withdrawn/Distinction) |

#### student_vle (Clean)
| Column | Type | Description |
|--------|------|-------------|
| code_module | STRING | Course module code |
| code_presentation | STRING | Course presentation |
| id_student | INTEGER | Student identifier |
| id_site | INTEGER | VLE resource identifier |
| date | DATE | Interaction date (converted from days) |
| sum_click | INTEGER | Number of clicks on resource |

#### student_assessments (Clean)
| Column | Type | Description |
|--------|------|-------------|
| id_assessment | INTEGER | Assessment identifier |
| id_student | INTEGER | Student identifier |
| date_submitted | DATE | Submission date |
| is_banked | INTEGER | Banking status (0/1) |
| score | DOUBLE | Assessment score (0-100) |

### Master View Schema

#### v_student_analytics_master
Comprehensive view combining all student data with derived metrics:

**Demographics & Enrollment**:
- Student info fields (gender, region, education, etc.)
- Registration and unregistration dates
- Course module and presentation details

**VLE Engagement Metrics**:
- `unique_resources_accessed`: Count of distinct VLE resources
- `total_vle_interactions`: Sum of all VLE interactions
- `total_clicks`: Total clicks across all resources
- `engagement_span_days`: Days between first and last interaction

**Assessment Performance**:
- `total_assessments_submitted`: Count of submitted assessments
- `assessments_passed`: Count of passing scores
- `average_score`: Mean score across all assessments
- Score breakdowns by type (TMA, CMA, Exam)

**Derived Analytics**:
- `pass_rate_percentage`: (Passed assessments / Total submitted) * 100
- `engagement_level`: Categorized as High/Medium/Low/No Engagement

---

## Technical Implementation Details

### Data Pipeline Architecture

```
Raw CSV Files → Databricks Upload → Raw Tables → Data Cleaning → Clean Tables → Analytical Views → Master Analytics View
```

### Key SQL Techniques Used

1. **Date Transformation**:
```sql
DATE_ADD('2013-02-01', TRY_CAST(date AS INT))
```

2. **Safe Type Casting**:
```sql
TRY_CAST(score AS DOUBLE) AS score
```

3. **Aggregation with Performance Metrics**:
```sql
ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2) as percentage
```

4. **Complex Joins with NULL Handling**:
```sql
LEFT JOIN eduanalytics.v_vle_engagement_summary ve 
    ON si.code_module = ve.code_module 
    AND si.code_presentation = ve.code_presentation 
    AND si.id_student = ve.id_student
```

### Performance Optimizations

- **Workspace Organization**: Structured database with clear naming conventions
- **View-Based Architecture**: Modular approach with reusable analytical views
- **Efficient Joins**: Strategic use of LEFT JOINs to preserve student records
- **Aggregation Optimization**: Pre-calculated metrics in views for faster querying
