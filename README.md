# OULAD Student Analytics: Key Insights & Findings

[Technical Documentation](https://github.com/tammai1610/edu-analytics/blob/main/Technical%20Documentation)

## Project Overview
**Objective**: Analyze 32,593 students across 7 modules to identify success patterns, predict at-risk students, and understand engagement drivers using comprehensive SQL-based analytics.

**Results**: 10 strategic queries examining performance, engagement, demographics, and risk factors with dashboard preparation for actionable insights.

## Critical Findings

[Queries and Insights - Full Report](https://github.com/tammai1610/edu-analytics/blob/main/03-analysis-dashboard.ipynb)

### 1. The Completion Crisis
Only **37.93% of students pass**, while **31.16% withdraw** before failing. Despite average assessment scores of 72.8%, less than half (47.2%) successfully complete programs.

**Key Insight**: This is a retention problem, not an academic standards issue.

### 2. Engagement Quality Over Quantity
High engagement students show **76.91% pass rate** with 3,423 average clicks, but surprisingly, "No Engagement" students still achieve **69% assessment scores**.

**Critical Discovery**: Engagement span (sustained interaction) correlates stronger with success (0.299) than total clicks (0.286) or resources accessed (0.239).

### 3. The Age Advantage
**Males aged 55+** achieve the highest success rate (71.26%) with lower platform engagement, while younger students (0-35) consistently underperform across genders.

**Strategic Insight**: Maturity translates to more efficient study habits requiring less platform interaction.

### 4. Educational Background as Risk Predictor
Students with **no formal qualifications** show 42.9% withdrawal rate versus 23.6% for post-graduates. The 40% cohort with below A-level education represents the highest-volume, highest-risk population.

### 5. Module Performance Gaps
**AAA module** leads with 70.99% success rate, while **CCC module** struggles at 37.84% despite moderate engagement and high enrollment.

**Assessment Intelligence**: Computer-marked assessments yield highest scores (78%) with lowest variability, while exams show most challenging performance (65.01%) with highest variability.

### 6. Early Warning Indicators
High-risk students show **42x lower platform interaction** (44 vs 1,887 clicks) and minimal assessment completion (1.84 vs 8.74 submissions).

**Risk Stratification**:
- High Risk: 206 students (0.8%) - Critical intervention needed
- Medium Risk: 6,664 students (25%) - Enhanced support required
- Low Risk: 19,857 students (73%) - Standard monitoring

## Strategic Recommendations

### Immediate Actions
1. **Deploy Early Warning System**: Automated alerts for students with <100 clicks in first 4 weeks
2. **Target CCC Module**: Immediate review and intervention for 37.84% success rate
3. **Support At-Risk Demographics**: Specialized programs for students without formal qualifications

### Medium-Term Strategy
1. **Engagement Quality Focus**: Shift metrics from clicks to sustained interaction patterns
2. **Age-Responsive Support**: Pair older successful students with younger learners
3. **Module Standardization**: Replicate AAA success factors across underperforming programs

### Success Metrics
- **Primary Goal**: Increase success rate from 47.2% to 60%
- **Secondary Goal**: Reduce withdrawal rate from 31.16% to <20%
- **Tertiary Goal**: Standardize module performance above 50% success rate

## Bottom Line
Strong academic performance among engaged students, but 53% completion failure indicates systemic retention challenges. The data reveals clear intervention opportunities through early warning systems, demographic-responsive support, and quality engagement strategies.
