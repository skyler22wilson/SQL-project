What issues will you address by cleaning the data?
1) Re-name certain columns where the column name does not make sense or is difficult to reference in a query
2) correct the data types for the columns. I imported all of the columns as charachter varying in order to make importing the data easier.
3) Normalize the database by assigning primary keys (if not already assigned), removing redundant columns/data and removing partial dependancies where possible




Queries:
Below, provide the SQL queries you used to clean your data.

//Begin the process of normalyzing the database

DELETE FROM analytics WHERE visit_number = 'visitNumber' 

DELETE FROM sales_by_sku
WHERE product_sku = 'productSKU'

//deleted columns with all NULL data or redundancy
ALTER TABLE analytics
DROP COLUMN user_id

ALTER TABLE analytics
DROP COLUMN visitor_id

//check if any user_id values are not null before dropping the column
SELECT COUNT(*) FROM analytics
WHERE user_id IS NOT NULL



ALTER TABLE analytics
ALTER COLUMN date TYPE DATE USING TO_DATE(date, 'YYYYMMDD'),
ALTER COLUMN visit_number TYPE INT USING visit_number::INT,
ALTER COLUMN unit_price TYPE NUMERIC USING unit_price::NUMERIC, 
ALTER COLUMN units_sold TYPE SMALLINT USING units_sold::SMALLINT, 
ALTER COLUMN visit_id TYPE INT USING visit_id::INT, 
ALTER COLUMN page_views TYPE SMALLINT USING page_views::SMALLINT
ALTER COLUMN bounces TYPE SMALLINT USING bounces::SMALLINT
ALTER COLUMN revenue TYPE NUMERIC USING revenue::NUMERIC
ALTER COLUMN time_on_site TYPE TIME USING time_on_site::TIME

UPDATE analytics
SET time_on_site = CAST(time_on_site || ' hours' AS INTERVAL)::TIME,
    units_sold = COALESCE(units_sold, 0),
    unit_price = unit_price / 100000,
    bounces = COALESCE(bounces, 0)

UPDATE analytics
SET units_sold = 0 
WHERE units_sold < 0

UPDATE analytics
SET unit_price = ROUND(unit_price, 2), 
	revenue = units_sold * unit_price

//converting visit_start_time to a TIMESTAMP
ALTER TABLE analytics
ADD COLUMN new_visit_start_time TIMESTAMP;

UPDATE analytics
SET new_visit_start_time = TO_TIMESTAMP(visit_start_time::bigint);

ALTER TABLE analytics
DROP COLUMN visit_start_time;


ALTER TABLE analytics
RENAME COLUMN new_visit_start_time TO visit_start_time;

//convberting data types in sales_by_sku
ALTER TABLE sales_by_sku
ALTER COLUMN total_ordered TYPE SMALLINT USING total_ordered::SMALLINT

//converting data types in products
ALTER TABLE products
ALTER COLUMN ordered_quantity TYPE SMALLINT USING ordered_quantity::SMALLINT, 
ALTER COLUMN stock_level TYPE SMALLINT USING stock_level::SMALLINT,
ALTER COLUMN sentiment_score TYPE FLOAT USING sentiment_score::FLOAT,
ALTER COLUMN sentiment_magnitude TYPE FLOAT USING sentiment_magnitude::FLOAT, 
ALTER COLUMN restocking_lead_time TYPE SMALLINT USING restocking_lead_time::SMALLINT

//converting datatypes for sales_report
ALTER TABLE sales_report
ALTER COLUMN total_ordered TYPE SMALLINT USING total_ordered::SMALLINT, 
ALTER COLUMN stock_level TYPE SMALLINT USING stock_level::SMALLINT,
ALTER COLUMN sentiment_score TYPE FLOAT USING sentiment_score::FLOAT,
ALTER COLUMN sentiment_magnitude TYPE FLOAT USING sentiment_magnitude::FLOAT, 
ALTER COLUMN restocking_lead_time TYPE SMALLINT USING restocking_lead_time::SMALLINT

//converting datatypes in all_sessions
ALTER TABLE all_sessions
ALTER COLUMN date TYPE DATE USING TO_DATE(date, 'YYYYMMDD'),
ALTER COLUMN total_transaction_revenue TYPE NUMERIC USING total_transaction_revenue::NUMERIC,
ALTER COLUMN transactions TYPE SMALLINT USING transactions::SMALLINT,
ALTER COLUMN page_views TYPE SMALLINT USING page_views::SMALLINT,
ALTER COLUMN session_quality TYPE SMALLINT USING session_quality::SMALLINT,
ALTER COLUMN refund_amount TYPE NUMERIC USING refund_amount::NUMERIC,
ALTER COLUMN product_price TYPE NUMERIC USING product_price::NUMERIC,
ALTER COLUMN product_revenue TYPE NUMERIC USING product_revenue::NUMERIC,
ALTER COLUMN product_quantity TYPE SMALLINT USING product_quantity::SMALLINT,
ALTER COLUMN item_revenue TYPE NUMERIC USING item_revenue::NUMERIC,
ALTER COLUMN item_quantity TYPE SMALLINT USING item_quantity::SMALLINT,
ALTER COLUMN ecommerce_type TYPE SMALLINT USING ecommerce_type::SMALLINT,
ALTER COLUMN ecommerce_step TYPE SMALLINT USING ecommerce_step::SMALLINT

UPDATE all_sessions
SET time_on_site = CAST(time_on_site || ' hours' AS INTERVAL)::TIME,
	time = CAST(time || 'hours' AS INTERVAL)::TIME,
	    city = CASE
			WHEN city = '(not set)' THEN NULL
			ELSE city
		    END,
	    country = CASE
			WHEN country = '(not set)' THEN NULL
			ELSE country
		      END,
	    variant = CASE
			WHEN variant = '(not set)' THEN NULL
			ELSE variant
		      END,
	    product_category = CASE
				WHEN product_category = '(not set)' THEN NULL
				ELSE product_category
			      END;
                      
// replaced city with country if city was null or not equal to a city name
UPDATE all_sessions
SET city = 
	CASE 
		WHEN city = 'not available in demo dataset' OR city IS NULL 
		AND country IS NOT NULL
		THEN country
	ELSE city
END

UPDATE all_sessions
SET type = LOWER(type)

SELECT item_quantity, item_revenue FROM all_sessions WHERE item_quantity IS NOT NULL OR item_revenue IS NOT NULL

ALTER TABLE all_sessions
DROP item_revenue,
DROP item_quantity,
DROP search_keyword,
DROP refund_amount


SET time = 
		CASE 
			WHEN time = '00:00:00' THEN NULL
			ELSE time
		END;

UPDATE all_sessions
SET product_revenue = ROUND(product_revenue/100000, 2)

// created a new table to separate transactions from all_sessions
CREATE TABLE transactions (
	transaction_id VARCHAR PRIMARY KEY,
	transactions VARCHAR,
	transaction_revenue NUMERIC,
	total_transaction_revenue NUMERIC,
	currency VARCHAR
)

// adding primary keys and connecting the tables in the database
ALTER TABLE analytics ADD COLUMN visit_number SERIAL

ALTER TABLE analytics
ADD CONSTRAINT pk_analytics PRIMARY KEY(visit_id, visit_number)

ALTER TABLE all_sessions ADD COLUMN visit_number SERIAL

ALTER TABLE all_sessions
ADD CONSTRAINT pk_all_sessions PRIMARY KEY(visit_id, visit_number)

//created a transactions table and a product_details table, 

// Created a unique_id for all_sessions and product_id that i could connect the two databases to

//cleaning the product details table
DELETE FROM product_details
WHERE visitor_id IS NULL;

ALTER TABLE all_sessions
ADD FOREIGN KEY(transaction_id) REFERENCES transactions(transaction_id)

