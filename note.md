# Customer Churn Dashboard

## Power Query Decisions

- Imported the Telco Customer Churn CSV file into Power BI.
- Set an explicit data type for each column in Power Query.
- `TotalCharges` was imported as text. There were 11 blank values for customers with 0 months of tenure, so I replaced those blanks with 0 before converting the column to a number. This keeps those customers in the analysis instead of removing them.
- `MonthlyCharges` was converted to a number using the `en-US` locale so that decimal values such as 29.85 were interpreted correctly.
- Created `TenureBand` with four groups: 0-12, 13-24, 25-48, and 49+ months.
- Calculated the first and third quartiles of `MonthlyCharges` as 35.5 and 89.85, then created `ChargeBand` with Low, Medium, and High groups.
- Created `ChurnFlag` as a numeric column where Yes = 1 and No = 0.
- The Power Query applied steps were kept in a logical order and named clearly.

## DAX Measures

The following measures were created explicitly in DAX:

- `Total Customers = COUNTROWS(Telco)`
- `Churned Customers = CALCULATE([Total Customers], Telco[Churn] = "Yes")`
- `Churn Rate % = DIVIDE([Churned Customers], [Total Customers])`
- `Avg Monthly Charges = AVERAGE(Telco[MonthlyCharges])`
- `Monthly Revenue at Risk = CALCULATE(SUM(Telco[MonthlyCharges]), Telco[Churn] = "Yes")`

`DIVIDE` is used instead of `/` so that a zero denominator returns a blank value instead of causing a division-by-zero error.

The `Churn Rate %` measure is calculated dynamically, so it changes correctly when the report is filtered or sliced.

## Dashboard

The report page contains:

- Total Customers: 7,043
- Churn Rate: 26.54%
- Monthly Revenue at Risk: approximately 139.13K USD
- Average Monthly Charges: 64.76 USD

The report includes churn rate charts by:

- Contract
- InternetService
- TenureBand
- PaymentMethod

Slicers were added for:

- Contract
- InternetService
- TenureBand

Cross-filtering was tested by selecting a value in the Contract slicer and confirming that the other visuals changed accordingly.

The report was created using Power BI Desktop on Windows.

## Segment Analysis

The dashboard shows several segments with relatively high churn rates and meaningful customer volumes:

- 0-12 months: 2,186 customers, 47.44% churn.
- Electronic check: 2,365 customers, 45.29% churn.
- Month-to-month contract: 3,875 customers, 42.71% churn.
- Fiber optic internet service: 3,096 customers, 41.89% churn.

For the main retention analysis, the two segments selected based on both churn rate and meaningful customer volume are:

1. **0-12 months:** 2,186 customers with a 47.44% churn rate.
2. **Electronic check:** 2,365 customers with a 45.29% churn rate.

These segments combine relatively high churn rates with a substantial number of customers, making them more useful for retention analysis than very small groups.

A 100% churn rate in a group of only 3 customers should not automatically be treated as a major retention problem. Although the percentage is high, the sample is too small to represent the same business impact or provide as reliable a basis for a retention action as a larger segment.

Month-to-month contracts and fiber optic customers also show high churn rates and meaningful customer volumes, so they are considered important areas for further retention analysis.

## Retention Recommendations

1. **Focus on early-stage customers:** Introduce an onboarding and early-retention program during the first 12 months, including regular check-ins, service guidance, and targeted offers before customers become more likely to churn.

2. **Address high churn among electronic-check customers:** Investigate the reasons for churn among customers using electronic check and test targeted retention actions. This segment should also be analyzed by contract type and tenure to identify more specific patterns.

3. **Reduce month-to-month and fiber-related churn:** Analyze month-to-month customers and fiber optic customers together with tenure and payment method, then use the findings to develop targeted retention offers or contract-conversion incentives.

## Technical Notes

- All data cleaning and transformation were performed in Power Query rather than Python or Excel before importing the data.
- DAX measures use `COUNTROWS`, `CALCULATE`, `AVERAGE`, `SUM`, and `DIVIDE`.
- Churn rate is implemented as an explicit DAX measure rather than a calculated column.
- The report uses Power BI Desktop on Windows.
- The dashboard was designed as a single-page interactive report with KPI cards, churn-rate visuals, and slicers.