What are your risk areas? Identify and describe them.

There are 4 potential risk areas that I have identified in the database:

The primary risk being that there is a lot of duplicate data within the database especially in the all_sessions table, and analytics table. The secondary and tertiary issues that I have identified are the data accuracy and validity. There are several columns in the all_sessions and analytics tables that did not conform to the expected structure such as the date column as well as price figures being far too high to be correct. I also wanted to assess data completeness, there were several columns with many NULL values. 



QA Process:
Describe your QA process and include the SQL queries used to execute it.

I wanted to check the uniqueness of certain values that I thought would be applicable primary keys for the database. 
For the all_sessions and product_details (I created this table) tables I was unable to generate unique values using the visitor ID so I created a unique ID for each row in the table. Additionally I checked the sku and product_sku values in the sales_report and products tables. 

--check if the visitor_id is unique
SELECT visit_id, COUNT(*) AS count 
FROM analytics 
GROUP BY visit_id
HAVING COUNT(*) > 1

--check to ensure the uniqueness of the primary key's in the tables

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

--create a unique key for the all_sessions and product_details table and connected the tables 
ALTER TABLE product_details ADD COLUMN id SERIAL PRIMARY KEY;
ALTER TABLE all_sessions ADD COLUMN id SERIAL PRIMARY KEY;
ALTER TABLE all_sesions FOREIGN KEY(unique_id) REFERENCES product_details(unique_id)

-- since multiple tables have shared information that is not redundant, I wanted to check to make sure that the shared information was consistent and if it was not, either correct it or find out why there was an inconsistancy

SELECT 'product_details' AS table_name,
	   COUNT(*) AS count_rows, 
	   product_sku AS key_name,
	   COUNT(DISTINCT(product_sku)) AS count_keys
FROM product_details
GROUP BY product_sku
UNION ALL
SELECT 'products' AS table_name,
	   COUNT(*) AS count_rows, 
	   sku AS key_name,
	   COUNT(DISTINCT(sku)) AS count_keys
FROM products
GROUP BY sku;


SELECT 'product_details' AS table_name,
	   COUNT(*) AS count_rows, 
	   unique_id AS key_name,
	   COUNT(DISTINCT(unique_id)) AS count_keys
FROM product_details
GROUP BY unique_id
HAVING COUNT(*) > 1
UNION ALL
SELECT 'all_sessions' AS table_name,
	   COUNT(*) AS count_rows, 
	   unique_id AS key_name,
	   COUNT(DISTINCT(unique_id)) AS count_keys
FROM all_sessions
GROUP BY unique_id
HAVING COUNT(*) > 1;


SELECT 'analytics' AS table_name,
	   COUNT(*) AS count_rows, 
	   visitor_id AS key_name,
	   COUNT(DISTINCT(visitor_id)) AS count_keys
FROM product_details
GROUP BY visitor_id
HAVING COUNT(*) > 1
UNION ALL
SELECT 'all_sessions' AS table_name,
	   COUNT(*) AS count_rows, 
	   visitor_id AS key_name,
	   COUNT(DISTINCT(visitor_id)) AS count_keys
FROM all_sessions
GROUP BY visitor_id
HAVING COUNT(*) > 1;

--checked which visitor_id's were identical in all_sessions and analytics to see if I could remove the duplicates without destroying the data integrity which I could not

SELECT a.visitor_id, a.table_name AS analytics_table, b.table_name AS all_sessions_table, b.count_rows AS repeated
	FROM (
		SELECT 'analytics' AS table_name,
			   COUNT(*) AS count_rows, 
			   visitor_id,
			   COUNT(DISTINCT(visitor_id)) AS count_keys
		FROM product_details
		GROUP BY visitor_id
		HAVING COUNT(*) > 1) a
	JOIN (
		SELECT 'all_sessions' AS table_name,
			   COUNT(*) AS count_rows, 
			   visitor_id,
			   COUNT(DISTINCT(visitor_id)) AS count_keys
		FROM all_sessions
		GROUP BY visitor_id
		HAVING COUNT(*) > 1) b
	ON a.visitor_id = b.visitor_id
  
  -- I want to look at data completeness in the context of what information in each table is absolutely necessary or relied upon in other tables, what data could be useful for further analysis that was not within the scope of this particular project and whether removing NULL values from columns and tables to ensure greater completeness in the tables would affect data integrity. 
  
-- I noticed the ecommerce_option adn ecommerce_step columns were related so I completed both columns based on the values in each table

UPDATE all_sessions
SET ecommerce_option = 
	CASE
		WHEN ecommerce_step = 1 THEN 'Billing and Shipping'
		WHEN ecommerce_step = 2 THEN 'Payment'
		WHEN ecommerce_step = 3 THEN 'Review'
		ELSE ecommerce_option
	END;
	
	

  
  




