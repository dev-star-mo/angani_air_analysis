# Angani Air: Turning Loyalty Data into Customer Growth

A Python data analytics project built for the **KeDataLab Python for Data Analytics** course. It analyses the customer loyalty and flight activity data of a fictional African airline, **Angani Air (AA)**, to assess whether a 2018 loyalty promotion created valuable customer behaviour.

> **Status: work in progress.** Data cleaning, feature engineering, campaign comparison tables and the first round of exploratory analysis are complete. Statistical testing, final conclusions and recommendations are still to come (see [Next steps](#next-steps)).

---

## Business problem

Angani Air ran a **Loyalty Program Promotion between February and April 2018**, which produced a large number of new sign-ups. Management wants to know:

1. **Did the campaign change customer behaviour?**
2. **Where should AA focus its future loyalty investment?**

## Data

Three CSV files (not included in this repo; place them in a `data/` folder):

| Dataset | Rows | Columns | Description |
|---|---|---|---|
| `Customer Loyalty History.csv` | 16,737 | 16 | One row per member: demographics, loyalty card tier, CLV, enrollment type and date, cancellation date |
| `Customer Flight Activity.csv` | 392,936 | 8 | Monthly flights, distance, points accumulated and redeemed per member (2017 to 2018) |
| `Calendar.csv` | 2,557 | 4 | Date lookup table (start of year, quarter and month) |

## What has been done so far

### 1. Data audit and cleaning
- Wrote a reusable `quality_audit()` function reporting missing values, data types, unique counts and duplicates for each table.
- Converted calendar dates to `datetime`.
- Found that **Salary is missing for ~25% of members** and that cancellation dates are missing for ~88% (these are customers who never cancelled). Missing values were handled so that they are not mistaken for real zeros: `0` is used as a placeholder for cancellation fields, and salary is restored to `NaN` with a `SalaryMissing` flag for analysis.
- Flagged **1,922 duplicate rows** in the flight activity data (see [Known issues](#known-issues)).

### 2. Merging
- Left-joined flight activity onto the customer loyalty table on `Loyalty Number` (customer table as the base), then confirmed there were no unmatched records.

### 3. Feature engineering
Features were built at the customer level, in these groups:

- **Time features:** activity date, quarter, month name, year-month, season, and cyclical month encoding (sine/cosine) for seasonality.
- **Travel behaviour:** total flights and distance, active vs inactive months, no-flight-month rate, average flights per month and per active month, average distance per flight.
- **Loyalty engagement:** points accumulated and redeemed, redemption rate, points per flight, cost per redeemed point, months with redemptions.
- **Activity timing:** each customer's last active month, months since last flight, and 2017 vs 2018 flight change (absolute and percentage).
- **Membership lifecycle:** enrollment and cancellation dates, cancelled/active flags, membership tenure in months, enrollment cohort, and a promotion-enrollment flag.
- **Customer profile:** salary bands and CLV segments (quartiles), loyalty card level (Aurora = 1, Nova = 2, Star = 3) and education level (ordinal, High School or Below = 1 up to Doctor = 5).

### 4. Campaign comparison
Built a like-for-like comparison of **promotion members vs standard members who enrolled in the same months (Feb to Apr 2018)**:
- A fixed **3-month follow-up window** (enrollment month plus the next two months) per customer, with missing activity treated as zero flights.
- Outcomes per customer: flights, distance, points accumulated and redeemed, active months, and whether they flew at all.
- A second view covering activity from enrollment through December 2018, **normalised by months observed**, so that February enrollees (more months of data) are compared fairly with April enrollees.

### 5. Exploratory data analysis (preliminary observations)
Charts of 2018 promotion enrollments broken down by gender, education, salary band, loyalty card level, marital status and CLV segment. Early observations:

- The promotion drew **971 new members in 2018** alongside 2,039 standard sign-ups, and the three promotion months saw more enrollments than the rest of the year.
- Customers **with degrees** made up most promotion sign-ups.
- **Lower-earning** customers signed up more than middle and higher earners.
- **Star** tier cards were the most common among promotion enrollees.
- **Married** customers signed up more than singles and divorced customers.

*These are descriptive counts. They show who signed up, not yet whether the campaign changed behaviour.*

## Tech stack

- Python 3.12+ (the notebook uses nested quotes in f-strings)
- `pandas` (a recent version, since the notebook shows the new `str` dtype), `numpy`
- `matplotlib`, `seaborn`, `plotly`
- Jupyter Notebook

## How to run

1. Clone this repository.
2. Create a `data/` folder and add the three CSV files listed above.
3. Update the file paths in the data-loading cell (currently absolute local paths) to point at your `data/` folder, for example `pd.read_csv('data/Calendar.csv')`.
4. Install dependencies: `pip install pandas numpy matplotlib seaborn plotly jupyter`
5. Open `project.ipynb` and run all cells.

## Known issues

- **Duplicate flight activity rows (1,922)** were identified but not yet removed. They should be reviewed and handled before the final analysis, as they can inflate flight and points totals.
- File paths are hard-coded to a local machine and need updating (see above).
- Some EDA cells filter on a column (for example `& (customer_loyalty['Gender'])`) in a way that triggers a pandas deprecation warning. It runs, but the filter is redundant and should be cleaned up.
- The EDA conclusions are visual only and are not yet backed by statistical tests.

## Next steps

- [ ] Remove or reconcile duplicate flight records.
- [ ] Compare promotion vs standard members on the outcome metrics above, with significance tests (for example t-test / Mann-Whitney) to see whether the differences are real.
- [ ] Compare cancellation and retention rates between promotion and standard members.
- [ ] Analyse redemption behaviour and points usage by segment.
- [ ] Identify which segments (tier, CLV, income, province) show the most valuable behaviour.
- [ ] Write the final conclusions and **recommendations on where to focus future loyalty investment**.
- [ ] Add a short executive summary at the top of the notebook.


Data Analyst / IoT Engineer, Nairobi, Kenya
Course: KeDataLab, Python for Data Analytics
