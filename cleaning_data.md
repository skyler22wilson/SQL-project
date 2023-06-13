What issues will you address by cleaning the data?
1) Re-name certain columns where the column name does not make sense or is difficult to reference in a query
2) correct the data types for the columns. I imported all of the columns as charachter varying in order to make importing the data easier.
3) remove null or redundant data and delete unneccesary columns from each table or redundant columns
4) ensure some form of normalization to the database (to the best of my ability) through removing partial dependancies and creating primary adn foreign keys where possible/where I am able.


Queries:
Below, provide the SQL queries you used to clean your data.

//Begin the process of normalyzing the database

-- Delete rows: these rows contained no data but rather had the original column names from the csv file and were not needed
DELETE FROM analytics WHERE visit_number = 'visitNumber';
DELETE FROM sales_by_sku WHERE product_sku = 'productSKU';

-- Remove columns with all NULL data or redundancy from analytics table
ALTER TABLE analytics DROP COLUMN IF EXISTS user_id; -- NULL
ALTER TABLE analytics DROP COLUMN IF EXISTS visit_id; -- redundant
ALTER TABLE all_sessions DROP COLUMN IF EXISTS visit_id --redundant
ALTER TABLE products DROP COLUMN IF EXISTS sentiment_score;
ALTER TABLE products DROP COLUMN IF EXISTS sentiment_magnitude;
ALTER TABLE sales_report DROP COLUMN IF EXISTS restocking_lead_time;


--Incorporated data from CSV with all columns as VARCHAR/charecter varying so that no data was lost in the import
--then converted all the columns in every table to the correct format

-- Convert data types in analytics table
ALTER TABLE analytics
    ALTER COLUMN date TYPE DATE USING TO_DATE(date, 'YYYYMMDD'),
    ALTER COLUMN visit_number TYPE INT USING visit_number::INT,
    ALTER COLUMN unit_price TYPE NUMERIC USING unit_price::NUMERIC, 
    ALTER COLUMN units_sold TYPE SMALLINT USING units_sold::SMALLINT, 
    ALTER COLUMN visit_id TYPE INT USING visit_id::INT, 
    ALTER COLUMN page_views TYPE SMALLINT USING page_views::SMALLINT,
    ALTER COLUMN bounces TYPE SMALLINT USING bounces::SMALLINT,
    ALTER COLUMN revenue TYPE NUMERIC USING revenue::NUMERIC,
    ALTER COLUMN time_on_site TYPE TIME USING time_on_site::TIME;

-- Update values in analytics table
UPDATE analytics
SET time_on_site = CAST(time_on_site || ' hours' AS INTERVAL)::TIME,
    units_sold = COALESCE(units_sold, 0),
    unit_price = unit_price / 100000,
    bounces = COALESCE(bounces, 0);

UPDATE analytics
SET units_sold = 0 
WHERE units_sold < 0;

UPDATE analytics
SET unit_price = ROUND(unit_price, 2), 
    revenue = units_sold * unit_price;

-- correctly format the visit_start_time in analytics table
ALTER TABLE analytics ADD COLUMN new_visit_start_time TIMESTAMP;

UPDATE analytics
SET new_visit_start_time = TO_TIMESTAMP(visit_start_time::bigint);

ALTER TABLE analytics DROP COLUMN visit_start_time;

ALTER TABLE analytics RENAME COLUMN new_visit_start_time TO visit_start_time;

-- Convert data types in sales_by_sku table
ALTER TABLE sales_by_sku ALTER COLUMN total_ordered TYPE SMALLINT USING total_ordered::SMALLINT;

-- Convert data types in products table
ALTER TABLE products
    ALTER COLUMN ordered_quantity TYPE SMALLINT USING ordered_quantity::SMALLINT,
    ALTER COLUMN stock_level TYPE SMALLINT USING stock_level::SMALLINT;


-- Convert data types in sales_report table
ALTER TABLE sales_report
    ALTER COLUMN total_ordered TYPE SMALLINT USING total_ordered::SMALLINT,
    ALTER COLUMN stock_level TYPE SMALLINT USING stock_level::SMALLINT;


-- Convert data types in all_sessions table
ALTER TABLE all_sessions
    ALTER COLUMN date TYPE DATE USING TO_DATE(date, 'YYYYMMDD'),
    ALTER COLUMN total_transaction_revenue TYPE NUMERIC USING total_transaction_revenue::NUMERIC,
    ALTER COLUMN transactions TYPE SMALLINT USING transactions::SMALLINT,
    ALTER COLUMN page_views TYPE SMALLINT USING page_views::SMALLINT,
    ALTER COLUMN session_quality TYPE SMALLINT USING session_quality::SMALLINT,
    ALTER COLUMN refund_amount TYPE NUMERIC USING refund_amount::NUMERIC,
    ALTER COLUMN product_price TYPE NUMERIC USING product_price::NUMERIC,
    ALTER COLUMN product_revenue TYPE NUMERIC USING product_revenue::NUMERIC,
    ALTER COLUMN product_quantity TYPE SMALLINT USING product_quantity::SM

