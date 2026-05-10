# 💳 Digital Wallet Customer Lifetime Value (LTV) Analysis Using Advanced SQL

<div align="center">

![MySQL](https://img.shields.io/badge/MySQL-Advanced-blue?style=for-the-badge\&logo=mysql)
![FinTech Analytics](https://img.shields.io/badge/Domain-FinTech%20Analytics-success?style=for-the-badge)
![Business Intelligence](https://img.shields.io/badge/Analytics-Business%20Intelligence-orange?style=for-the-badge)
![SQL Case Study](https://img.shields.io/badge/Project-SQL%20Case%20Study-red?style=for-the-badge)

</div>

---

# 📌 Introduction

Customer Lifetime Value (LTV) is one of the most important metrics in the digital wallet and fintech ecosystem.

While many businesses focus only on transaction volume, long-term profitability depends on understanding:

* customer engagement
* transaction behavior
* retention patterns
* satisfaction trends
* spending consistency
* app usage behavior
* issue resolution efficiency

This project presents an advanced SQL-driven analysis of digital wallet customer behavior using customer-level financial and engagement data.

The objective was not just to write SQL queries, but to extract business intelligence that can help fintech companies improve:

* customer retention
* monetization
* engagement strategy
* operational efficiency
* customer satisfaction
* revenue optimization

---

# 🎯 Stakeholder Focus

This analysis is designed for:

## FinTech & Digital Wallet Companies

Including:

* Customer Success Teams
* Product Teams
* Revenue Teams
* Retention Analysts
* Business Intelligence Teams
* Growth Strategy Teams

---

# 📂 Dataset Overview

The dataset contains customer-level digital wallet behavioral data including:

* Customer Lifetime Value (LTV)
* Total Spending
* Transaction Frequency
* Average Transaction Value
* App Usage Frequency
* Active Days
* Support Tickets Raised
* Customer Satisfaction Scores
* Payment Preferences
* Issue Resolution Time
* Last Transaction Activity
* Income Segments
* Geographic Locations

Each row represents a customer profile enriched with financial and behavioral KPIs.

---

# 🧠 Analytical Framework

The project uses advanced SQL concepts to solve real-world business problems.

Key SQL concepts used:

* Common Table Expressions (CTEs)
* Window Functions
* Ranking Functions
* Aggregations
* Segmentation Analysis
* Cohort-style Behavioral Analysis
* Comparative KPI Analysis

---

# ⚙️ Advanced SQL Functions Used

## ✅ Window Functions

Implemented:

* `DENSE_RANK()`
* `LAG()`
* `NTILE()`
* Running Comparisons

---

## ✅ Aggregate Functions

Used:

* `SUM()`
* `AVG()`
* `COUNT()`
* `ROUND()`

---

## ✅ Analytical SQL Techniques

* Customer Segmentation
* Retention Analysis
* Satisfaction Analysis
* Revenue Optimization
* Behavioral Comparison
* Exception Detection
* Ranking & Performance Analysis

---

# 📊 Key Insights & Analysis

---

# 1️⃣ Identify customers who spend more than the average customer but have fewer active days than the average.

## 🎯 Why this matters

Helps identify highly valuable customers who generate strong revenue despite limited engagement.

---

## 💻 SQL Query

```sql
select customer_id,total_spent,active_days,
(select avg(total_spent) from customers)as Total_avg,
(select avg(active_days)from customers)as Total_avg_days
from customers
where total_spent > (
select avg(total_spent)as Total_Avg from customers)and
active_days < (
select avg(active_days)from customers);
```

---

## 💡 Insight

These customers may represent premium or high-intent users who transact efficiently despite lower platform activity.

---

# 2️⃣ Find the top customers in each income level based on lifetime value and show how much higher they are compared to others in the same group.

## 🎯 Why this matters

Highlights high-value customer concentration within income segments.

---

## 💻 SQL Query

```sql
select *,
case
when prv is null then "-"
else cast(prv-ltv as char)
end as diff
from (
select customer_id,income_level,ltv,
dense_rank()over(partition by income_level order by ltv desc)as Ltv_Rank,
lag(ltv) over (partition by income_level )as Prv
From customers)t;
```

---

## 💡 Insight

The analysis helps identify dominant customers within each income category and measure competitive gaps.

---

# 3️⃣ Determine which age group contributes the highest total spend within each location.

## 🎯 Why this matters

Supports geo-demographic targeting and regional customer strategy.

---

## 💻 SQL Query

```sql
with high_rank as (
select Location,age,total_spent,
dense_rank()over(partition by location order  by total_spent desc)as spent_rank
from (
select location,age, round(sum(total_spent),2)as Total_spent
from customers
group by location,age)t)
select Location,age,total_spent
from high_rank
where spent_rank =1
order by total_spent desc;
```

---

## 💡 Insight

Different age groups dominate spending behavior across locations, enabling localized marketing optimization.

---

# 4️⃣ Identify customers whose most recent transaction was much older than others but still rank high in total spending.

## 🎯 Why this matters

Detects potentially churned high-value customers.

---

## 💻 SQL Query

```sql
select customer_id,total_spent,last_transaction_days_ago,spent_rank,recency_rank
from (
select
customer_id,total_spent,last_transaction_days_ago,
dense_rank()over(order by total_spent desc)as spent_rank,
dense_rank()over(order by last_transaction_days_ago desc )as recency_rank
from customers)t
where spent_rank <=10 and
recency_rank <=10;
```

---

## 💡 Insight

These users may require urgent retention campaigns because they historically generated high value but recently became inactive.

---

# 5️⃣ Compare highest support tickets with those who raise minimum tickets, and determine how their satisfaction scores differ.

## 🎯 Why this matters

Measures the relationship between customer support burden and user satisfaction.

---

## 💻 SQL Query

```sql
select support_tickets_raised,avg(customer_satisfaction_score)as avg_satisfaction_score
from customers
group by support_tickets_raised
order by support_tickets_raised desc
limit 2;
```

---

## 💡 Insight

Customers raising frequent support tickets often display different satisfaction behavior, helping identify service quality gaps.

---

# 6️⃣ Find customers whose average transaction value is consistently high even though their total number of transactions is low.

## 🎯 Why this matters

Helps identify premium users who generate large-value transactions with lower frequency.

---

## 💻 SQL Query

```sql
select customer_id, avg_transaction_value,total_transactions,avg_transac_rank,total_transac_rank
from (
select customer_id, avg_transaction_value,total_transactions,
dense_rank()over(order by avg_transaction_value desc)as avg_transac_rank,
dense_rank()over(order by total_transactions asc)as total_transac_rank
from customers)t
order by avg_transac_rank asc;
```

---

## 💡 Insight

High-value low-frequency users may require a completely different retention and rewards strategy.

---

# 7️⃣ Within each app usage category, identify customers who spend significantly more than others in the same category.

## 🎯 Why this matters

Detects outlier spenders within behavioral engagement groups.

---

## 💻 SQL Query

```sql
select  customer_id, App_Usage_Frequency,total_spent,
round((total_spent-prv)*100/prv,2)as Prc_Less_than_prv
from (
select customer_id, App_Usage_Frequency,total_spent,
dense_rank() over (partition by App_Usage_Frequency order by total_spent desc)as Spent_rank,
coalesce(lag(total_spent) over (partition by App_Usage_Frequency),"-")as prv
from customers)
t;
```

---

## 💡 Insight

Even within similar app engagement categories, spending behavior varies drastically.

---

# 8️⃣ Determine whether customers with longer tenure always have higher lifetime value, and identify exceptions.

## 🎯 Why this matters

Challenges the assumption that customer longevity always equals profitability.

---

## 💻 SQL Query

```sql
select customer_id, active_days,ltv, tenure_rank,ltv_rank
from (
select customer_id, active_days,ltv,
dense_rank () over (order by active_days desc)as tenure_rank,
dense_rank () over (order by ltv desc)as ltv_rank
from customers)t
where tenure_rank <=20 and ltv_rank >= 100;
```

---

## 💡 Insight

Some long-tenure customers contribute surprisingly low lifetime value, revealing retention inefficiencies.

---

# 9️⃣ Rank customers within each income level based on average transaction value and identify the top performers.

## 🎯 Why this matters

Helps identify financially valuable customer segments.

---

## 💻 SQL Query

```sql
with transac_rank as (
select customer_id, income_level,avg_transaction_value,
dense_rank () over (partition by income_level order by avg_transaction_value desc)as Avg_transac_rank
from customers)
select customer_id, income_level,avg_transaction_value,avg_transac_rank
from transac_rank
where avg_transac_rank <=10;
```

---

## 💡 Insight

Top spenders differ significantly across income groups, helping improve customer segmentation strategies.

---

# 🔟 Identify customers whose issue resolution time is worse than average but who still report high satisfaction scores.

## 🎯 Why this matters

Measures resilience of customer satisfaction despite operational inefficiencies.

---

## 💻 SQL Query

```sql
with rnks as (
select customer_id,issue_resolution_time,customer_satisfaction_score,
dense_rank() over (order by issue_resolution_time desc)as Resoltuion_Rank,
dense_rank () over (order by customer_satisfaction_score desc)as Satisfaction_rank
from customers)
SELECT customer_id,issue_resolution_time,customer_satisfaction_score,Resoltuion_Rank,Satisfaction_rank
from rnks
where Resoltuion_Rank <=10 and Satisfaction_rank <=10;
```

---

## 💡 Insight

Certain users remain highly satisfied despite operational delays, indicating strong product loyalty.

---

# 1️⃣1️⃣ Find payment methods that are preferred by high-value customers and compare their average lifetime value.

## 🎯 Why this matters

Helps identify payment behaviors associated with premium customers.

---

## 💻 SQL Query

```sql
select customer_id,preferred_payment_method,round(sum(ltv),2)as Total_LTV,
(select round(avg(ltv),2) from customers)as Avg_LTV
from customers
group by customer_id,preferred_payment_method
order by sum(ltv)desc
limit 10;
```

---

## 💡 Insight

Certain payment methods may correlate strongly with higher-value customer segments.

---

# 1️⃣2️⃣ Determine which income group has the highest proportion of customers with low satisfaction scores but high spending.

## 🎯 Why this matters

Detects financially valuable but potentially at-risk customer groups.

---

## 💻 SQL Query

```sql
WITH ranked_customers AS (
    SELECT
        customer_id,
        income_level,
        total_spent,
        customer_satisfaction_score,
        NTILE(4) OVER (ORDER BY total_spent DESC) AS spend_quartile,
        AVG(customer_satisfaction_score) OVER () AS avg_satisfaction
    FROM customers
)
, income_summary AS (
    SELECT
        income_level,
        COUNT(*) AS total_customers,
        SUM(
            CASE
                WHEN spend_quartile = 1
                AND customer_satisfaction_score < avg_satisfaction
                THEN 1
                ELSE 0
            END
        ) AS unhappy_high_spenders
    FROM ranked_customers
    GROUP BY income_level
)
SELECT
    income_level,
    unhappy_high_spenders,
    total_customers,
    unhappy_high_spenders / total_customers AS proportion
FROM income_summary
ORDER BY proportion DESC;
```

---

## 💡 Insight

High-spending dissatisfied customers represent a major churn and revenue risk.

---

# 1️⃣3️⃣ Identify customers who are inactive recently but historically have very high engagement and spending.

## 🎯 Why this matters

Detects dormant but historically valuable users for reactivation campaigns.

---

## 💻 SQL Query

```sql
SELECT
    customer_id,
    total_spent,
    total_transactions,
    active_days,
    last_transaction_days_ago,
    inactivity_rank,
    spend_rank,
    engagement_rank
FROM (
    SELECT
        customer_id,
        total_spent,
        total_transactions,
        active_days,
        last_transaction_days_ago,
        DENSE_RANK() OVER (
            ORDER BY last_transaction_days_ago DESC
        ) AS inactivity_rank,
        DENSE_RANK() OVER (
            ORDER BY total_spent DESC
        ) AS spend_rank,
        DENSE_RANK() OVER (
            ORDER BY active_days DESC
        ) AS engagement_rank
    FROM customers
) t
WHERE inactivity_rank <= 20
  AND spend_rank <= 20     
  AND engagement_rank <= 20;
```

---

## 💡 Insight

These customers likely represent the highest-value reactivation opportunities.

---

# 1️⃣4️⃣ Compare lifetime value trends across customers with different app usage frequencies and identify which group shows the strongest performance.

## 🎯 Why this matters

Measures how engagement frequency impacts long-term customer value.

---

## 💻 SQL Query

```sql
WITH ltv_summary AS (
    SELECT
        app_usage_frequency,
        COUNT(*) AS total_customers,
        AVG(ltv) AS avg_ltv,
        SUM(ltv) AS total_ltv
    FROM customers
    GROUP BY app_usage_frequency
),
top_ltv_customers AS (
    SELECT
        app_usage_frequency,
        AVG(ltv) AS avg_ltv_top_customers
    FROM (
        SELECT
            app_usage_frequency,
            ltv,
            DENSE_RANK() OVER (
                PARTITION BY app_usage_frequency
                ORDER BY ltv DESC
            ) AS ltv_rank
        FROM customers
    ) t
    WHERE ltv_rank <= 10
    GROUP BY app_usage_frequency
)
SELECT
    s.app_usage_frequency,
    s.total_customers,
    s.avg_ltv,
    s.total_ltv,
    t.avg_ltv_top_customers
FROM ltv_summary s
LEFT JOIN top_ltv_customers t
    ON s.app_usage_frequency = t.app_usage_frequency
ORDER BY s.avg_ltv DESC;
```

---

## 💡 Insight

Frequent app usage strongly correlates with higher lifetime value and stronger customer monetization.

---

# 📈 Overall Business Impact

This project demonstrates how SQL analytics can help digital wallet companies:

✅ Improve customer retention
✅ Detect churn-risk customers
✅ Optimize engagement strategies
✅ Identify premium customer segments
✅ Improve monetization decisions
✅ Measure operational efficiency
✅ Support customer satisfaction analysis
✅ Build data-driven growth strategies

---

# 🚀 Why This Project Stands Out

✔ Real-world fintech analytics use case
✔ Advanced SQL implementation
✔ Strong business-oriented analysis
✔ Customer behavior intelligence
✔ Retention and engagement analysis
✔ Revenue optimization insights
✔ Recruiter-friendly SQL case study

---

# 👨‍💻 Author

## Karan Patel

Aspiring Data Analyst | SQL | Power BI | Business Intelligence | FinTech Analytics

<div align="center">

### ⭐ If you found this project useful, consider giving it a star!

</div>
