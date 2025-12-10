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

USE Test_DB;
GO

SELECT @@SERVERNAME;

SELECT * 
FROM INFORMATION_SCHEMA.TABLES 
WHERE TABLE_NAME = 'Customer_shopping_behaviour';
