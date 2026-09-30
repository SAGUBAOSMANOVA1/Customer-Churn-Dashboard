# Customer Churn Dashboard

## Power Query Decisions

- Imported the Telco Customer Churn CSV file into Power BI.
- Set the data type for each column in Power Query.
- `TotalCharges` was imported as text. There were 11 blank values for customers with 0 months of tenure, so I replaced those blanks with 0 before converting the column to a number.
- `MonthlyCharges` was converted to a number using the `en-US` locale so that decimal values such as 29.85 were interpreted correctly.
- Created `TenureBand` with four groups: 0-12, 13-24, 25-48, and 49+ months.
- Calculated the first and third quartiles of `MonthlyCharges` as 35.5 and 89.85, then created `ChargeBand` with Low, Medium, and High groups.
- Created `ChurnFlag` as a numeric column where Yes = 1 and No = 0.

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
- Monthly Revenue at Risk: approximately 139.13K
- Average Monthly Charges: 64.76

The report also includes churn rate charts by:

- Contract
- InternetService
- TenureBand
- PaymentMethod

Slicers were added for:

- Contract
- InternetService
- TenureBand

Cross-filtering was tested by selecting a value in the Contract slicer and checking that the other visuals changed accordingly.

## Segment Analysis

The following segments showed relatively high churn rates with meaningful customer volumes:

- Month-to-month contract: 3,875 customers, 42.71% churn rate.
- Fiber optic internet service: 3,096 customers, 41.89% churn rate.
- Electronic check payment method: 2,365 customers, 45.29% churn rate.
- 0-12 months tenure: 2,186 customers, 47.44% churn rate.

For the main retention analysis, two segments were selected based on both churn rate and customer volume:

1. Month-to-month customers — 3,875 customers with a 42.71% churn rate.
2. Customers with 0-12 months of tenure — 2,186 customers with a 47.44% churn rate.

These segments have relatively high churn and also represent a meaningful number of customers, so they are more useful for retention analysis than very small groups.

A 100% churn rate in a group of only 3 customers should not automatically be treated as a major retention problem. The percentage is high, but the group is too small to have the same business impact as a larger segment with a high churn rate.

## Retention Recommendations

1. **Focus on early-stage customers:** Introduce an onboarding and early-retention program during the first 12 months, with check-ins, service guidance, and targeted offers before customers become likely to churn.

2. **Reduce month-to-month churn:** Give month-to-month customers incentives to move to longer-term contracts, such as discounts or additional benefits for switching to one-year or two-year plans.

3. **Target high-risk payment and service combinations:** Pay special attention to customers using electronic check and customers with fiber optic service. Analyze these groups together with contract type and tenure to identify targeted retention offers instead of applying the same action to all customers.

## Technical Notes

- Data cleaning and transformation were performed in Power Query rather than Python.
- DAX measures use `COUNTROWS`, `CALCULATE`, `AVERAGE`, `SUM`, and `DIVIDE`.
- Churn rate is implemented as an explicit DAX measure rather than a calculated column.
- The report was created in Power BI Desktop on Windows.