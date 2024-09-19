# superstore-sales-analysis
This repository contains a data analytics project focused on analyzing sales performance and customer behavior using SQL. The dataset used for this project is from a superstore and includes various dimensions such as sales, profit, customer details, and product information.

USE project;

-- Initial Data Exploration and Cleaning

-- What does the data look like?
SELECT * FROM superstore LIMIT 10;

-- Are there any missing values in the dataset?
SELECT 
  SUM(CASE WHEN Order_ID IS NULL THEN 1 ELSE 0 END) AS Missing_Order_ID,
  SUM(CASE WHEN Customer_Name IS NULL THEN 1 ELSE 0 END) AS Missing_Customer_Name,
  SUM(CASE WHEN Sales IS NULL THEN 1 ELSE 0 END) AS Missing_Sales
FROM superstore;

-- Are there any duplicate records in the dataset?
SELECT Order_ID, COUNT(*)
FROM superstore
GROUP BY Order_ID
HAVING COUNT(*) > 1;

-- Sales Analysis

-- What are the total sales and profit?
SELECT 
  SUM(Sales) AS Total_Sales,
  SUM(Profit) AS Total_Profit
FROM superstore;

-- Which category and sub-category have the highest sales?
SELECT 
  Category, 
  Sub_category, 
  SUM(Sales) AS Total_Sales
FROM superstore
GROUP BY Category, Sub_category
ORDER BY Total_Sales DESC;

-- Which product is generating the most profit?
SELECT 
  Product_Name, 
  SUM(Profit) AS Total_Profit
FROM superstore
GROUP BY Product_Name
ORDER BY Total_Profit DESC
LIMIT 10;

-- Customer Analysis

-- Who are the top customers based on sales?
SELECT 
  Customer_Name, 
  SUM(Sales) AS Total_Sales
FROM superstore
GROUP BY Customer_Name
ORDER BY Total_Sales DESC
LIMIT 10;

-- What is the average sales per customer segment?
SELECT 
  Segment, 
  AVG(Sales) AS Avg_Sales
FROM superstore
GROUP BY Segment
ORDER BY Avg_Sales DESC;

-- Regional Analysis
SELECT 
  Region, 
  SUM(Sales) AS Total_Sales,
  SUM(Profit) AS Total_Profit
FROM superstore
GROUP BY Region
ORDER BY Total_Sales DESC;

-- Which state contributes the most to sales?
SELECT 
  State, 
  SUM(Sales) AS Total_Sales
FROM superstore
GROUP BY State
ORDER BY Total_Sales DESC
LIMIT 10;

-- Shipping and Time Analysis

-- What is the average time taken to ship orders?
SELECT 
  AVG(DATEDIFF(Ship_Date, Order_Date)) AS Avg_Shipping_Time
FROM superstore;

-- How does the shipping mode impact sales?
SELECT 
  Ship_Mode, 
  COUNT(Order_ID) AS Number_Of_Orders, 
  SUM(Sales) AS Total_Sales
FROM superstore
GROUP BY Ship_Mode
ORDER BY Total_Sales DESC;

-- Discount Impact Analysis

-- What is the impact of discounts on profit?
SELECT 
  Discount, 
  SUM(Sales) AS Total_Sales, 
  SUM(Profit) AS Total_Profit
FROM superstore
GROUP BY Discount
ORDER BY Discount;

-- Creating a temporary view for Sales Metrics
CREATE VIEW Sales_Metrics AS
SELECT 
    Order_ID,
    Sales,
    Quantity,
    Discount,
    Profit
FROM superstore;

-- Creating a temporary view for Order Details
CREATE VIEW Order_Details AS
SELECT 
    Order_ID,
    Order_Date,
    Ship_Date,
    Ship_Mode,
    Customer_ID,
    Customer_Name,
    Segment,
    Product_ID,
    Category,
    Sub_category,
    Region,
    State
FROM superstore;

-- Joining the Order Details and Sales Metrics views
SELECT 
    od.Order_ID,
    od.Order_Date,
    od.Customer_Name,
    od.Category,
    od.Sub_category,
    sm.Sales,
    sm.Profit
FROM Order_Details od
JOIN Sales_Metrics sm
    ON od.Order_ID = sm.Order_ID
ORDER BY sm.Sales DESC;
ORDER BY sm.Sales DESC;
