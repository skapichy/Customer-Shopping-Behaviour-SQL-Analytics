# Customer-Shopping-Behaviour-SQL-Analytics
This project analyzes Customer Shopping Behaviour using Microsoft SQL Server and also the goal is to demonstrate how SQL transforms raw customer data into powerful business insights.
It showcases real-world SQL skills including:
* Database exploration
* Aggregations & KPIs
* Window functions
* Subqueries
* Segmentation logic
* Revenue analysis
* Product performance ranking
* Discount impact analysis
* Behavioral insights

# Tools Used
* Microsoft SQL Server
* SSMS (SQL Server Management Studio)
* SQL Analytical Functions
* Window Functions (ROW_NUMBER)
* Subqueries & CTEs
* Aggregations & CASE expressions
  
## Database & Table Check
```TSQL
USE Test_DB;
GO
SELECT @@SERVERNAME;
SELECT * 
FROM INFORMATION_SCHEMA.TABLES 
WHERE TABLE_NAME = 'Customer_shopping_behaviour';
```
* Confirms you're connected to the right server.
* Ensures the dataset exists before running analysis.
* Prevents running queries on the wrong environment
  
## Query: Filter Customers by Size & Payment Method
```TSQL
SELECT *
FROM Customer_shopping_behaviour
WHERE size = 'L'
  AND payment_method IN ('Cash', 'Venmo');
```  
* Helps identify buying patterns of size-L customers.
* Useful for inventory planning, POS optimization, and preferred-payment promotions.

## Total Revenue by Gender
```TSQL
SELECT 
    SUM(purchase_amount) AS Total_revenue,
    gender
FROM Customer_shopping_behaviour
GROUP BY gender;
```
* Shows whether male or female customers generate more revenue.
* Helps marketing teams target the high-value gender segment.
* Useful for campaign targeting & brand positioning.

## Discount Users Who Spend Above Average
```TSQL
SELECT 
    customer_id, 
    purchase_amount
FROM Customer_shopping_behaviour
WHERE discount_applied = 'Yes'
  AND purchase_amount >= (
        SELECT AVG(purchase_amount) 
        FROM Customer_shopping_behaviour);
```
* Finds customers who still spend big even when discounts are applied.
* These are “Quality Discount Buyers” — likely to respond to premium offers.
* Helps design profitable loyalty or voucher programs.

## Top 10 Products with Highest Review Ratings
```TSQL
SELECT TOP(10) 
    item_purchased,
    ROUND(AVG(review_rating), 2) AS highestReviewRating
FROM Customer_shopping_behaviour
GROUP BY item_purchased
ORDER BY highestReviewRating DESC;
```
* Reveals customer-loved products.
* Helps marketing teams push top-rated items in ads.
* Guides procurement on what products to stock heavily.

## Compare Average Spend: Standard vs Express Shipping
```TSQL
SELECT 
    shipping_type,
    AVG(purchase_amount) AS [average purchase amount]
FROM Customer_shopping_behaviour
GROUP BY shipping_type
HAVING shipping_type IN ('Standard', 'Express');
```
* Shows whether customers who choose Express shipping spend more.
* Helps logistics teams adjust shipping promotions.
* Useful for free-shipping thresholds.

## Do Subscribed Customers Spend More?
```TSQL
SELECT 
    subscription_status,
    COUNT(customer_id) AS totalCustomer,
    ROUND(AVG(purchase_amount), 4) AS averageSpent,
    SUM(purchase_amount) AS totalRevenue
FROM Customer_shopping_behaviour
GROUP BY subscription_status;
```
* Measures subscription model profitability.
* Indicates if subscribers are high-value customers.
* Helps management justify investments in subscription programs.

## Top 5 Discounted Products
```TSQL
SELECT TOP(5)
    item_purchased,
    COUNT(frequency_of_purchases) AS [frequency of purchases]
FROM Customer_shopping_behaviour
GROUP BY item_purchased, discount_applied
HAVING discount_applied = 'Yes';
```
* Identifies products customers buy mostly when discounted.
* Useful for pricing strategy, seasonal deals, and inventory clearance.

## Products With Highest Discount Rates
```TSQL
SELECT TOP(5)
    item_purchased,
    ROUND(
        100 * SUM(CASE WHEN discount_applied = 'Yes' THEN 1 ELSE 0 END) / COUNT(*),
        2
    ) AS discount_rate
FROM Customer_shopping_behaviour
GROUP BY item_purchased
ORDER BY discount_rate DESC;
```
* Shows products where demand is highly discount-dependent.
* Helps identify items that may not sell at full price.
* Supports decision-making on clearance, price drops, or supplier renegotiations.

## Customer Segmentation (New, Returning, Loyal)
```TSQL
WITH customer_type AS (
    SELECT 
        customer_id,
        previous_purchases,
        CASE
            WHEN previous_purchases = 1 THEN 'New'
            WHEN previous_purchases BETWEEN 2 AND 10 THEN 'Returning'
            ELSE 'Loyal'
        END AS customer_segment
    FROM Customer_shopping_behaviour
)
SELECT 
    customer_segment,
    COUNT(*) AS [Number of Customers]
FROM customer_type
GROUP BY customer_segment;
```
* Breaks customers into behavioral groups.
* Helps build targeted marketing:
  * New → welcome discount
  * Returning → loyalty programs
  * Loyal → VIP treatment

## Top 3 Products per Category
```TSQL
WITH item_counts AS (
    SELECT 
        item_purchased,
        category,
        COUNT(customer_id) AS total_orders,
        ROW_NUMBER() OVER(PARTITION BY Category ORDER BY COUNT(customer_id) DESC) AS RankedItem
    FROM Customer_shopping_behaviour
    GROUP BY category, item_purchased
)
SELECT 
    RankedItem,
    item_purchased,
    category,
    total_orders
FROM item_counts
WHERE RankedItem <= 3;
```
* Helps identify category leaders.
* Useful for product display decisions, ads, and stock allocation.

## Are Repeat Buyers Likely to Subscribe?
```TSQL
SELECT 
    subscription_status,
    COUNT(customer_id) AS repeat_buyers
FROM Customer_shopping_behaviour
WHERE previous_purchases > 5
GROUP BY subscription_status;
```
* Shows relationship between loyalty and subscription.
* Helps subscription teams understand adoption patterns.

## Revenue by Age Group
```TSQL
SELECT 
    age_group,
    SUM(previous_purchases) AS total_revenue
FROM Customer_shopping_behaviour
GROUP BY age_group
ORDER BY total_revenue;
```
* Identifies the highest-spending age groups.
* Guides marketing on target age demographics.

## Top 3 Products by Category & Age Group
```TSQL
WITH items_count AS (
    SELECT 
        age_group,
        item_purchased,
        category,
        COUNT(customer_id) AS total_orders,
        ROW_NUMBER() OVER(
            PARTITION BY category, age_group 
            ORDER BY COUNT(customer_id) DESC
        ) AS Rank_items
    FROM Customer_shopping_behaviour
    GROUP BY age_group, item_purchased, category
)
SELECT 
    Rank_items,
    category,
    age_group,
    item_purchased,
    total_orders
FROM items_count
WHERE Rank_items <= 3;
```
* Shows top items by both age group and category.
* Supports hyper-targeted marketing and recommendation engines.
