This `README.md` is structured to showcase your ability to move from high-level business metrics to granular operational details. It uses clean Markdown formatting and includes the code blocks exactly as you requested.

---

# Brazilian E-Commerce Public Dataset Analysis

### SQL Business Intelligence Project

## 📌 Project Overview

This project involves a comprehensive analysis of the Brazilian E-Commerce Public Dataset using **PostgreSQL**. The objective was to extract meaningful business insights regarding revenue performance, customer behavior, and logistical efficiency across various Brazilian regions.

---

## 📊 1. Financial Performance (The North Star)

Understanding the total scale of the marketplace by calculating the total revenue generated.

```sql
SELECT to_char(sum(payment_value) / 1000000.0, 'FM999,999.00')
as "Total Revenue (in Millions)" 
FROM order_payments;

```

### Revenue by Product Category

Breaking down sales to identify which categories contribute most to the platform's growth.

```sql
SELECT 
    r.product_category_name_english AS "Product Category",
    to_char(
        (SUM(q.price + q.freight_value) / 1000), 
        'FM9,999,999.0'
    ) AS "Total Revenue (in K)"
FROM products p
INNER JOIN order_items q 
    ON p.product_id = q.product_id
INNER JOIN product_category_name_translation r
    ON r.product_category_name = p.product_category_name
GROUP BY 1 
ORDER BY SUM(q.price + q.freight_value) DESC;

```

---

## 💳 2. Payment & Customer Insights

Analyzing how customers interact with the platform and identifying top-tier contributors.

### Payment Method Distribution

```sql
SELECT 
    payment_type AS "Payment Type", 
    count(payment_type) AS "Total Transactions"
FROM order_payments
GROUP BY payment_type
ORDER BY "Total Transactions" DESC;

```

### Top 10 Customers by Purchase Value

Identifying "Power Users" by their total spending habits.

```sql
SELECT 
    a.customer_id as "Customer ID",
    to_char(c.payment_value, 'FM99,999.0') as "Total Purchase Value (R$)"
FROM customers a
JOIN orders b ON a.customer_id = b.customer_id
JOIN order_payments c ON b.order_id = c.order_id
ORDER BY c.payment_value DESC
LIMIT 10;

```

---

## ⭐ 3. Customer Satisfaction & Feedback

Monitoring the quality of the customer experience through ratings and response agility.

### Average Order Rating

```sql
SELECT round(avg (review_score ::INT),2) as "Avg Order Rating" FROM order_reviews;

```

### Review Response Latency

Measuring how long it takes for feedback to be processed.

```sql
SELECT 
    TO_CHAR(
        JUSTIFY_INTERVAL(AVG(review_answer_timestamp - review_creation_date)), 
        'DD "days" HH24:MI'
    ) AS "Avg. Review Time"
FROM order_reviews;

```

---

## 🚚 4. Logistics & Regional Operations

Deep-diving into the geographical distribution of orders and the impact of product weight on delivery speed.

### Regional Order Volume

Using custom mapping to group 27 states into 5 strategic macro-regions.

```sql
SELECT 
    CASE 
        WHEN a.customer_state IN ('SP', 'RJ', 'MG', 'ES') THEN 'Southeast'
        WHEN a.customer_state IN ('PR', 'SC', 'RS') THEN 'South'
        WHEN a.customer_state IN ('MT', 'MS', 'GO', 'DF') THEN 'Central-West'
        WHEN a.customer_state IN ('BA', 'PE', 'CE', 'RN', 'PB', 'AL', 'SE', 'MA', 'PI') THEN 'Northeast'
        WHEN a.customer_state IN ('AM', 'RR', 'AP', 'PA', 'TO', 'RO', 'AC') THEN 'North'
        ELSE 'Unknown'
    END AS "Region",
    COUNT(b.order_id) AS "Total Orders"
FROM customers a
JOIN orders b ON a.customer_id = b.customer_id
GROUP BY 1
ORDER BY 2 DESC;

```

### Delivery Time vs. Product Weight

Investigating if shipping logistics are affected by the physical weight of the products.

```sql
SELECT DISTINCT ROUND(a.product_weight_g / 1000) AS "Weight (kg)",
TO_CHAR(AVG(b.order_delivered_customer_date - b.order_purchase_timestamp), 
'DD "Day" HH24:MI') AS "Delivery Time"
FROM products a
INNER JOIN order_items c ON a.product_id = c.product_id
INNER JOIN orders b ON b.order_id = c.order_id
WHERE a.product_weight_g IS NOT NULL
GROUP BY 1
ORDER BY 1 DESC;

```

---

## 🛠️ Technical Skills Demonstrated

* **Database Management:** PostgreSQL / pgAdmin 4
* **Data Aggregation:** `SUM`, `AVG`, `COUNT`
* **Complex Logic:** `CASE` Statements, Data Type Casting (`::INT`)
* **Time Series:** `JUSTIFY_INTERVAL`, Timestamp subtraction
* **Data Joining:** `INNER JOIN`, `LEFT JOIN` across multiple relational tables

---

## 📬 Contact

**LinkedIn:** https://www.linkedin.com/in/mohammad-amir-b93a26397/

---

**Would you like me to help you draft the final LinkedIn post now that your GitHub structure is ready?**
