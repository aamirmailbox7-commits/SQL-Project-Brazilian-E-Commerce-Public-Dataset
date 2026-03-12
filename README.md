Brazilian E-Commerce Public Dataset Analysis
SQL Business Intelligence Project (Handling 1M+ Records)
📌 Project Overview
This project features a comprehensive end-to-end analysis of the Brazilian E-Commerce Public Dataset using PostgreSQL. The primary focus was transforming raw, large-scale data into high-level business intelligence regarding revenue, customer lifecycle, and complex logistics.

📂 Data Scale & Source
Data Volume: This analysis was performed on a production-scale relational database containing 1,000,000+ rows of transactional data.

Source: Kaggle - Olist Brazilian E-Commerce Dataset

Technical Note: Due to the large file size (exceeding GitHub's 25MB limit), raw CSV files are not hosted in this repository. All insights were derived using optimized SQL queries within a PostgreSQL environment.

📊 1. Financial Performance (The North Star)
Understanding the total scale of the marketplace by calculating the total revenue generated.

SQL
SELECT to_char(sum(payment_value) / 1000000.0, 'FM999,999.00')
as "Total Revenue (in Millions)" 
FROM order_payments;
💳 2. Payment & Customer Insights
Analyzing transaction types and identifying high-value "Power Users."

SQL
-- Payment Method Distribution
SELECT 
    payment_type AS "Payment Type", 
    count(payment_type) AS "Total Transactions"
FROM order_payments
GROUP BY payment_type
ORDER BY "Total Transactions" DESC;

-- Top 10 Customers by Purchase Value
SELECT 
    a.customer_id as "Customer ID",
    to_char(c.payment_value, 'FM99,999.0') as "Total Purchase Value (R$)"
FROM customers a
JOIN orders b ON a.customer_id = b.customer_id
JOIN order_payments c ON b.order_id = c.order_id
ORDER BY c.payment_value DESC
LIMIT 10;
🚚 3. Logistics & Regional Operations
Deep-diving into the geographical distribution of orders across Brazil.

SQL
-- Regional Order Volume Mapping
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
📦 4. Delivery Efficiency vs. Product Weight
Investigating how physical product attributes impact shipping speed and customer wait times.

SQL
SELECT DISTINCT ROUND(a.product_weight_g / 1000) AS "Weight (kg)",
TO_CHAR(AVG(b.order_delivered_customer_date - b.order_purchase_timestamp), 
'DD "Day" HH24:MI') AS "Delivery Time"
FROM products a
INNER JOIN order_items c ON a.product_id = c.product_id
INNER JOIN orders b ON b.order_id = c.order_id
WHERE a.product_weight_g IS NOT NULL
GROUP BY 1
ORDER BY 1 DESC;
⭐ 5. Customer Satisfaction & Feedback
Monitoring the quality of the customer experience through ratings and response agility.

SQL
-- Avg Order Rating
SELECT round(avg (review_score ::INT),2) as "Avg Order Rating" FROM order_reviews;

-- Review Response Latency
SELECT 
    TO_CHAR(
        JUSTIFY_INTERVAL(AVG(review_answer_timestamp - review_creation_date)), 
        'DD "days" HH24:MI'
    ) AS "Avg. Review Time"
FROM order_reviews;
🛠️ Technical Skills Demonstrated
Database Management: PostgreSQL / pgAdmin 4

Data Aggregation: SUM, AVG, COUNT, ROUND

Complex Logic: CASE Statements, Data Type Casting (::INT)

Time Series: JUSTIFY_INTERVAL, Timestamp manipulation/subtraction

Data Joining: Advanced INNER JOIN logic across multiple relational tables
## 

📬 Contact

**LinkedIn:** https://www.linkedin.com/in/mohammad-amir-b93a26397/
