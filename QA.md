What are your risk areas? Identify and describe them.
There are 4 potential risk areas that I have identified in the database:
The primary risk being that there is a lot of duplicate data within the database especially in the all_sessions table, and analytics table. The secondary and tertiary issues that I have identified are the data accuracy and validity. There are several columns in the all_sessions and analytics tables that did not conform to the expected structure such as the date column as well as price figures being far too high to be correct. I also wanted to assess data completeness, there were several columns with many NULL values. 



QA Process:
Describe your QA process and include the SQL queries used to execute it.

I wanted to check the uniqueness of certain values that I thought would be applicable primary keys for the database. 
For the all_sessions and product_details (I created this table) tables I was unable to generate unique values using the visitor ID so I created a unique ID for each row in the table. Additionally I checked the sku and product_sku values in the sales_report and products tables. 

//check if the visitor_id is unique
SELECT visit_id, COUNT(*) AS count 
FROM analytics 
GROUP BY visit_id
HAVING COUNT(*) > 1

//check the uniqueness of the primary key's in the tables

SELECT sku, COUNT(*) AS count 
FROM products
GROUP BY sku
HAVING COUNT(*) > 1

SELECT product_sku, COUNT(*) AS count 
FROM sales_report
GROUP BY product_sku
HAVING COUNT(*) > 1

SELECT unique_id, COUNT(*) AS count 
FROM product_details
GROUP BY unique_id
HAVING COUNT(*) > 1

SELECT unique_id, COUNT(*) AS count 
FROM all_sessions
GROUP BY unique_id
HAVING COUNT(*) > 1

//create a unique key for the all_sessions and product_details table
