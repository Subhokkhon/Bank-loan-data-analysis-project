# 🏦 Bank Loan Report Analysis — SQL | Power BI | Python | Excel

An end-to-end analysis of a bank's lending portfolio. The same set of KPIs and charts is built in **four tools** (SQL Server, Power BI, Python, Excel) so the results can be cross-checked and compared.

---

## 📌 Table of Contents
- [Project Overview](#-project-overview)
- [Problem Statement](#-problem-statement)
- [Repository Structure](#-repository-structure)
- [Dataset Description](#-dataset-description)
- [Tools & Technologies](#-tools--technologies)
- [KPI Definitions](#-kpi-definitions)
- [SQL Analysis](#-sql-analysis)
- [Python Analysis](#-python-analysis)
- [Excel Analysis](#-excel-analysis)
- [Power BI Dashboard](#-power-bi-dashboard)
- [Key Results](#-key-results)
- [How to Run](#-how-to-run)
- [Skills Demonstrated](#-skills-demonstrated)

---

## 🎯 Project Overview

- Built a **Bank Loan Report** to monitor and assess the bank's lending activity and portfolio health.
- Tracks loan volume, funded amount, repayments, interest rate and borrower risk (DTI) over time.
- Separates the portfolio into **Good Loans** and **Bad Loans** to measure loan quality.
- Delivers **3 dashboards** (Summary, Overview, Details) in Power BI, with matching analysis in SQL, Python and Excel.
- Covers the full analyst workflow: domain understanding → data exploration → KPI calculation → visualization → reporting.

---

## 📝 Problem Statement

- Monitor the bank's lending performance and identify trends that can guide lending strategy.
- **Dashboard 1 – Summary:** headline KPIs with Month-to-Date (MTD) and Month-over-Month (MoM) change, Good vs Bad loan KPIs, and a Loan Status grid view.
- **Dashboard 2 – Overview:** six charts breaking down Applications, Funded Amount and Amount Received by month, state, term, employment length, purpose and home ownership.
- **Dashboard 3 – Details:** a consolidated, drill-down table of all loan records and borrower profiles.

---

## 📂 Repository Structure

```
├── data/
│   ├── financial_loan.csv                # Raw dataset (CSV, used for SQL import)
│   └── financial_loan_data_excel.xlsx    # Excel workbook (data + dashboards)
├── sql/
│   └── bankloanprojectquery.sql          # All SQL queries (KPIs, Good/Bad loans, grouped analysis)
├── python/
│   └── BankLoanAnalysis.ipynb            # Jupyter notebook (pandas, matplotlib, plotly)
├── powerbi/
│   └── bank_loan_data.pbix               # Power BI report (3 dashboard pages)
├── docs/
│   ├── ProblemStatement.docx             # Business requirements & KPI/chart specs
│   ├── Domain_Knowledge_Doc.docx         # Banking / loan domain background
│   ├── Terminologies_in_data.docx        # Data dictionary of fields
│   └── Query_Doc.docx                    # Reference SQL query document
└── README.md
```

---

## 🗃️ Dataset Description

- **Size:** 38,576 loan records × 24 columns.
- **Period covered:** 1 Jan 2021 – 12 Dec 2021 (based on `issue_date`).
- **Missing values:** only `emp_title` has nulls (1,438 rows); all other columns are complete.
- **Geography:** 50 US states (`address_state`).
- **Loan terms:** 36 months and 60 months.
- **Loan statuses:** Fully Paid, Current, Charged Off.

| Category | Columns |
|---|---|
| Identifiers | `id`, `member_id` |
| Borrower profile | `address_state`, `emp_length`, `emp_title`, `home_ownership`, `annual_income`, `verification_status`, `application_type` |
| Loan details | `purpose`, `term`, `loan_amount`, `int_rate`, `installment`, `grade`, `sub_grade` |
| Risk / credit | `dti`, `total_acc`, `last_credit_pull_date` |
| Dates | `issue_date`, `last_payment_date`, `next_payment_date` |
| Performance | `loan_status`, `total_payment` |

- `int_rate` and `dti` are stored as decimals, so they are multiplied by 100 to display percentages.
- Date columns are in **DD-MM-YYYY** format.
- A full field-by-field explanation (purpose + how banks use each field) is in `Terminologies_in_data.docx`.

---

## 🛠️ Tools & Technologies

- **SQL Server (T-SQL)** – aggregations, `CASE WHEN`, `GROUP BY`, date functions (`MONTH`, `DATENAME`).
- **Power BI** – data modelling, interactive dashboards, filled map, treemap, donut, area and bar charts, slicers, page navigation.
- **Python** – `pandas`, `numpy`, `matplotlib`, `seaborn`, `plotly`, run in Jupyter / Google Colab.
- **Microsoft Excel** – pivot tables, pivot charts, slicers, dashboard design sheet.
- **Documentation** – Word docs for requirements, domain knowledge and data dictionary.

---

## 📐 KPI Definitions

- **Total Loan Applications** – `COUNT(id)`
- **Total Funded Amount** – `SUM(loan_amount)`
- **Total Amount Received** – `SUM(total_payment)`
- **Average Interest Rate** – `AVG(int_rate) × 100`
- **Average DTI** – `AVG(dti) × 100`
- **MTD** – metrics for the latest month in the data (**December 2021**).
- **PMTD** – metrics for the previous month (**November 2021**), used to calculate **MoM change**.
- **Good Loan** – `loan_status` is `Fully Paid` or `Current`.
- **Bad Loan** – `loan_status` is `Charged Off`.
- **Good / Bad Loan %** – loans in that category ÷ total applications × 100.

---

## 🗄️ SQL Analysis

File: `sql/bankloanprojectquery.sql` (table name: `bank_loan_data`)

**1. Summary KPIs**
- Total Applications, Total Funded Amount, Total Amount Received.
- Average Interest Rate and Average DTI.

**2. MTD & PMTD (MoM) KPIs**
- Same KPIs filtered with `MONTH(issue_date) = 12` (MTD) and `= 11` (PMTD).
- Covers Applications, Funded Amount, Amount Received, Avg Interest Rate and Avg DTI.

**3. Good Loan vs Bad Loan**
- Good Loan %, applications, funded amount and amount received.
- Bad Loan %, applications, funded amount and amount received.
- Percentages calculated with `COUNT(CASE WHEN …)` × 100.0 ÷ `COUNT(id)`.

**4. Loan Status Grid View**
- `GROUP BY loan_status` returning loan count, total received, total funded, avg interest rate and avg DTI.
- A separate MTD grid (December) showing funded amount and amount received per status.

**5. Overview Dashboard Queries (grouped analysis)**
- **Monthly trend** – by `MONTH(issue_date)` and `DATENAME(MONTH, …)`.
- **Regional** – by `address_state`.
- **Loan term** – by `term`.
- **Employment length** – by `emp_length`.
- **Loan purpose** – by `purpose`.
- **Home ownership** – by `home_ownership`.
- Each returns Total Applications, Total Funded Amount and Total Amount Received.

**6. Additional Drill-down**
- Loan purpose analysis filtered to **Grade A** borrowers (`WHERE grade = 'A'`).

---

## 🐍 Python Analysis

File: `python/BankLoanAnalysis.ipynb`

**Setup & Exploration**
- Imported `pandas`, `numpy`, `matplotlib`, `seaborn`, `plotly`.
- Loaded the Excel dataset with `pd.read_excel`.
- Inspected shape, `info()`, `dtypes`, `describe()` and `head()`.

**KPI Calculations**
- Total Applications, Funded Amount, Amount Received (reported in $ millions).
- MTD metrics computed dynamically from the latest `issue_date` (year + month filter).
- Average Interest Rate and Average DTI.
- Good Loan and Bad Loan metrics using `.isin()` filters (applications, funded, received, %).

**Visualizations (each shown for Applications, Funded Amount and Amount Received)**
- **Monthly trend** – filled line / area charts with data labels.
- **State-wise** – horizontal bar charts.
- **Loan term** – pie / donut charts (36 vs 60 months).
- **Employment length** – horizontal bar charts.
- **Loan purpose** – horizontal bar charts.
- **Home ownership** – interactive `plotly` treemaps.

---

## 📊 Excel Analysis

File: `data/financial_loan_data_excel.xlsx`

- **`financial_loan`** sheet – cleaned source data (38,576 rows).
- **`Design Sheet`** – pivot tables and calculations that feed the dashboards.
- **`Summary Dashboard`** – KPI cards, Good vs Bad loan view and charts with slicers.
- **`Overview Dashboard`** – pivot charts for month, state, term, employment length, purpose and home ownership.
- Interactive slicers for filtering by key dimensions.

---

## 📈 Power BI Dashboard

File: `powerbi/bank_loan_data.pbix`

**Page 1 – Summary**
- KPI cards: Total Applications, Funded Amount, Amount Received, Avg Interest Rate, Avg DTI.
- MTD values with MoM comparison for each KPI.
- Good Loan vs Bad Loan KPI cards and donut charts.
- Loan Status grid (table) with applications, funded, received, MTD figures, interest rate and DTI.
- Slicers and page navigator.

**Page 2 – Overview**
- **Area chart** – monthly trends by issue date.
- **Filled map** – metrics by US state.
- **Donut chart** – loan term split.
- **Bar charts** – employment length and loan purpose.
- **Treemap** – home ownership.
- Slicers and page navigator for cross-filtering.

**Page 3 – Details**
- Detailed table of loan records and borrower information.
- Slicers for filtering the data.

**Design**
- Custom background image, consistent layout, KPI card styling and navigation buttons between pages.

---

## 🔍 Key Results

Figures computed from the full dataset (Jan–Dec 2021):

- **Total Loan Applications:** 38,576
- **Total Funded Amount:** ≈ $435.76 M
- **Total Amount Received:** ≈ $473.07 M
- **Average Interest Rate:** ≈ 12.05 %
- **Average DTI:** ≈ 13.33 %
- **Good Loans:** 33,243 applications (**86.18 %**) – funded ≈ $370.22 M, received ≈ $435.79 M
- **Bad Loans:** 5,333 applications (**13.82 %**) – funded ≈ $65.53 M, received ≈ $37.28 M
- **MTD (Dec 2021):** 4,314 applications, ≈ $54.0 M funded, ≈ $58.1 M received, 12.36 % avg interest rate, 13.67 % avg DTI
- **PMTD (Nov 2021):** 4,035 applications, ≈ $47.8 M funded, ≈ $50.1 M received, 11.94 % avg interest rate, 13.30 % avg DTI
- **Insights**
  - Applications, funded amount and collections rose month over month in December.
  - Debt consolidation is the most common loan purpose (18,214 loans), followed by credit card refinancing.
  - 36-month loans make up the large majority (28,237 vs 10,339 for 60 months).
  - California, New York and Florida lead in application volume.
  - Charged-off loans recovered only about 57 % of the amount funded, so they are the main source of portfolio loss.

---

## ▶️ How to Run

- **SQL**
  - Import `financial_loan.csv` into SQL Server as table `bank_loan_data` (set `issue_date` to `DATE`).
  - Run `bankloanprojectquery.sql` query by query.
- **Python**
  - `pip install pandas numpy matplotlib seaborn plotly openpyxl`
  - Open `BankLoanAnalysis.ipynb` and update the file path to `financial_loan_data_excel.xlsx`.
  - Run all cells.
- **Power BI**
  - Open `bank_loan_data.pbix` in Power BI Desktop (use Refresh if the data source path needs updating).
- **Excel**
  - Open `financial_loan_data_excel.xlsx` and explore the dashboard sheets and slicers.

---

## 💡 Skills Demonstrated

- Business requirement analysis and KPI design
- Domain understanding of bank lending and credit risk
- SQL aggregation, conditional logic and date handling
- Data cleaning, exploration and visualization with Python
- Pivot-table and dashboard building in Excel
- Interactive dashboard design and storytelling in Power BI
- Cross-validating results across multiple tools

---

## 🙌 Acknowledgements

- Project brief and domain documents based on the *Data Tutorials* Bank Loan Report project.

---

⭐ If you found this project useful, please give the repository a star!
