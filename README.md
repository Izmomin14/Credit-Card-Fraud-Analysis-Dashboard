# Credit-Card-Fraud-Analysis-Dashboard
Credit Card Fraud Analysis Dashboard (BFSI)

An interactive Power BI dashboard that analyses credit card transactions to find where and when fraud happens. Data was cleaned using Python (Pandas and NumPy), stored in SQL Server, and the dashboard built in Power BI covers fraud rate, risky categories, peak fraud hours, amount patterns, customer age groups, states and repeat-fraud customers across 2 pages (2024-2025).

<details> <summary><h2>📌 Problem Statement</h2></summary>

- A bank issues credit cards across India, but has no single view of which transactions are fraudulent and where fraud is concentrated
- Fraud patterns by merchant category, transaction amount, time of day, state and customer age group are not analysed, so risky segments cannot be identified
- Fraud transactions are much higher in value than genuine ones, so each missed fraud causes a large financial loss
- Fraud rate rises sharply at night, but there is no hour-wise view to support time-based alerts and monitoring
- Raw data had duplicates, mixed date and amount formats and missing values, so it was unreliable for decision making

</details>

<details> <summary><h2>🗂️ Data & Tables</h2></summary>

The dataset is synthetic (created with Python for practice, with intentional errors added) and consists of 3 related tables:

**Customers** cust_id, name, gender, dob, job, city, state, country, age, age_group

**Merchants** merchant_id, merchant_name, category, city, state, country

**Transactions** trans_id, trans_date_time, trans_date, trans_time, hour, cust_id, merchant_id, amt, is_fraud

Record counts (raw): Customers 1,045 | Merchants 218 | Transactions 12,345
After cleaning: ~1,000 customers | ~200 merchants | ~12K transactions | Fraud rate ~18% (increased for analysis, real-life fraud is much lower) | Period 2024-2025

</details>

<details> <summary><h2>🔗 Data Model (Relationships)</h2></summary>

- Transactions[cust_id] → Customers[cust_id]
- Transactions[merchant_id] → Merchants[merchant_id]
- Date[Date] → Transactions[trans_date]

</details>

<details> <summary><h2>🧹 Data Cleaning (Python - Pandas & NumPy)</h2></summary>

About 23% of the raw rows had errors. Cleaning steps:

- Removed duplicate records (e.g. repeated `trans_id`) and rows with no `cust_id`
- Fixed text columns: trimmed spaces, fixed casing (`str.strip`, `str.title`), corrected spelling variants and typos (e.g. Bangalore → Bengaluru, Trivandrum → Thiruvananthapuram, Government Employee)
- Converted blank and fake values (`?`, `-`, `N/A`, `null`, spaces) to proper nulls and filled missing city, state and country using other records
- Converted mixed date formats to one format with `pd.to_datetime`, removed future and impossible dates and invalid dates of birth (age outside 18-100)
- Created `age` (as on 01-01-2025) and `age_group` (Young, Adult, Middle, Senior), and split transaction date-time into date, time and hour
- Cleaned `amt` (removed ₹, Rs., INR and commas), fixed negative values, removed extreme outliers and filled missing amounts with the median amount of fraud / genuine group
- Standardized `is_fraud` values (Yes / No / 1 / 0 / True / False) and handled missing labels
- Loaded the cleaned tables into SQL Server and connected them to Power BI

</details>

<details> <summary><h2>🗺️ Dashboard Planning</h2></summary>

Before building, a dashboard map was designed to decide what the fraud team needed to see, mapping each problem statement point to a KPI or chart. This ensured every visual directly answers a business question instead of being added arbitrarily.

Each page has 4 KPIs + 1 slicer + 4 charts, with the same theme, card style and layout.

</details>

<details> <summary><h2>⚠️ Page 1: Fraud Overview</h2></summary>

**KPIs:** Total Txns · Fraud Txns · Fraud Rate % · Fraud Amt

**Slicer:** Year

**Charts:**

1. Fraud Rate by Category (Top 7)
2. Total Transactions and Fraud Transactions by Month
3. Fraud Rate % and Total Transactions by Hour
4. Fraud Rate by Age Group

**Key Findings:**

- About 12K transactions were analysed, with around 2,000 fraud cases (17.9% fraud rate) and a fraud loss of ₹37.67M
- Online categories have the highest fraud rate: Misc Net (~52%) and Shopping Net (~38%), followed by Travel (~31%)
- Fraud is very high at night: 60-90% of transactions between 12 AM and 4 AM are fraud, it falls below 10% after 6 AM and starts rising again after 9 PM
- Fraud transactions are much bigger in value (median about ₹10.7K vs about ₹1K for genuine ones)
- Monthly fraud stays fairly steady through the year, with small peaks in March, May and December
- Senior customers show the highest fraud rate among age groups

</details>

<details> <summary><h2>📍 Page 2: Customer & Location Risk</h2></summary>

**KPIs:** Avg Fraud Amt · Avg Normal Amt · Fraud Share % · Fraud Victims

**Slicer:** Category

**Charts:**

1. Fraud Rate by State
2. Top 10 Customers by Fraud Amount
3. Top 10 Merchants by Fraud Count
4. (Add your 4th chart here)

**Key Findings:**

- (Add after Page 2 visuals are final: top risky states, share of fraud amount from the top customers, and merchants with the most fraud cases)

</details>

<details> <summary><h2>🧮 Key DAX Measures</h2></summary>

```dax
Total Txns = COUNTROWS(Transactions)

Fraud Txns = CALCULATE(COUNTROWS(Transactions), Transactions[is_fraud] = "Yes")

Fraud Rate % = DIVIDE([Fraud Txns], [Total Txns])

Fraud Amt = CALCULATE(SUM(Transactions[amt]), Transactions[is_fraud] = "Yes")

Avg Fraud Amt = CALCULATE(AVERAGE(Transactions[amt]), Transactions[is_fraud] = "Yes")

Avg Normal Amt = CALCULATE(AVERAGE(Transactions[amt]), Transactions[is_fraud] = "No")

Fraud Share % = DIVIDE([Fraud Amt], SUM(Transactions[amt]))

Fraud Victims =
CALCULATE(
    DISTINCTCOUNT(Transactions[cust_id]),
    Transactions[is_fraud] = "Yes",
    Transactions[cust_id] <> "Unknown"
)
```

</details>

<details> <summary><h2>💡 Suggestions</h2></summary>

- Add extra checks (OTP, lower limits, instant alerts) for night-time transactions
- Use extra verification on online categories like Misc Net, Shopping Net and Travel
- Review high-value transactions (above about ₹5K), mainly at night
- Send instant SMS alerts and awareness messages to senior customers
- Closely watch customers and merchants with repeated fraud
- Use hour, category and amount together as a risk score instead of single rules, so fewer genuine transactions are blocked

</details>

<details> <summary><h2>🛠️ Tools Used</h2></summary>

- Python (Pandas, NumPy): data creation, cleaning and transformation
- SQL Server: data storage
- Power BI: data modeling, DAX measures, dashboard design

</details>

<details> <summary><h2>✅ Outcome</h2></summary>

The dashboard gives the bank a single, clear view of credit card fraud, replacing unreliable raw data. The fraud team can see which categories, hours, amount ranges and customer groups carry the most risk, and use these insights to set night-time alerts, extra verification rules and customer awareness campaigns.

</details>

<details> <summary><h2>👤 About Me</h2></summary>

**Momin Izhar Nisar**
Aspiring Data Analyst| Python · Power BI · SQL · Excel

- 📞 Phone: 9834011201
- 📧 Email: mominizhar45@gmail.com
- 💻 GitHub: [github.com/Izmomin14](https://github.com/Izmomin14)

</details>
