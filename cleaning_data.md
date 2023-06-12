What issues will you address by cleaning the data?
1) Re-name certain columns where the column name does not make sense or is difficult to reference in a query
2) correct the data types for the columns. I imported all of the columns as charachter varying in order to make importing the data easier.
3) Normalize the database by assigning primary keys (if not already assigned), removing redundant columns/data and removing partial dependancies




Queries:
Below, provide the SQL queries you used to clean your data.

//was unable to upload the CSV files correctly w/o the column names matchng exactly, thus, this process of renaming had to be done for most columns in every table

ALTER TABLE analytics
RENAME COLUMN "visitNumber" TO visit_number;

ALTER TABLE analytics
RENAME COLUMN "visitId" TO visit_id;

ALTER TABLE analytics
RENAME COLUMN "visitStartTime" TO visit_start_time;

ALTER TABLE analytics
RENAME COLUMN "userid" TO user_id;

ALTER TABLE analytics
RENAME COLUMN "channelGrouping" TO channel_grouping;

ALTER TABLE analytics
RENAME COLUMN "socialEngagementType" TO social_engagement_type;

ALTER TABLE analytics
RENAME COLUMN "pageviews" TO pageviews;

ALTER TABLE analytics
RENAME COLUMN "timeonsite" TO time_on_site;

ALTER TABLE products
RENAME COLUMN "orderedQuantity" TO ordered_quantity;

ALTER TABLE products
RENAME COLUMN "stockLevel" TO stock_level;

ALTER TABLE products
RENAME COLUMN "restockingLeadTime" TO restocking_lead_time;

ALTER TABLE products
RENAME COLUMN "sentimentMagnitude" TO sentiment_magnitude;

ALTER TABLE products
RENAME COLUMN "sentimentScore" TO sentiment_score;

ALTER TABLE all_sessions
RENAME COLUMN "channelGrouping" TO channel_grouping;

ALTER TABLE all_sessions
RENAME COLUMN "totalTransactionRevenue" TO total_transaction_revenue;

ALTER TABLE all_sessions
RENAME COLUMN "timeOnSite" TO time_on_site;

ALTER TABLE all_sessions
RENAME COLUMN "pageviews" TO page_views;

ALTER TABLE all_sessions
RENAME COLUMN "sessionQualityDim" TO session_quality

ALTER TABLE all_sessions
RENAME COLUMN "visitId" TO visit_id

ALTER TABLE all_sessions
RENAME COLUMN "productRefundAmount" TO product_refund_amount

ALTER TABLE all_sessions
RENAME COLUMN "productQuantity" TO product_quantity

ALTER TABLE all_sessions
RENAME COLUMN "productPrice" TO product_price

ALTER TABLE all_sessions
RENAME COLUMN "productRevenue" TO product_revenue

ALTER TABLE all_sessions
RENAME COLUMN "productSKU" TO product_sku

ALTER TABLE all_sessions
RENAME COLUMN "v2ProductName" TO product_name

ALTER TABLE all_sessions
RENAME COLUMN "v2ProductCategory" TO product_category

ALTER TABLE all_sessions
RENAME COLUMN "productVariant" TO product_variant

ALTER TABLE all_sessions
RENAME COLUMN "currencyCode" TO currency

ALTER TABLE all_sessions
RENAME COLUMN "itemQuantity" TO item_quantity

ALTER TABLE all_sessions
RENAME COLUMN "itemRevenue" TO item_revenue

ALTER TABLE all_sessions
RENAME COLUMN "transactionRevenue" TO transaction_revenue

ALTER TABLE all_sessions
RENAME COLUMN "transactionId" TO transaction_id

ALTER TABLE all_sessions
RENAME COLUMN "pageTitle" TO page_title

ALTER TABLE all_sessions
RENAME COLUMN "searchKeyword" TO search_keyword

ALTER TABLE all_sessions
RENAME COLUMN "pagePathLevel1" TO page_path

ALTER TABLE all_sessions
RENAME COLUMN "eCommerceAction_type" TO action_type

ALTER TABLE all_sessions
RENAME COLUMN "eCommerceAction_step" TO action_step

ALTER TABLE all_sessions
RENAME COLUMN "eCommerceAction_option" TO action_option

ALTER TABLE all_sessions
RENAME COLUMN "fullVisitorId" TO full_visitor_id

ALTER TABLE sales_by_sku
RENAME COLUMN "productSKU" TO product_sku

ALTER TABLE sales_report
RENAME COLUMN "productSKU" TO product_sku

ALTER TABLE sales_report
RENAME COLUMN "stockLevel" TO stock_level

ALTER TABLE sales_report
RENAME COLUMN "restockingLeadTime" TO restocking_lead_time

ALTER TABLE sales_report
RENAME COLUMN "sentimentScore" TO sentiment_score

ALTER TABLE sales_report
RENAME COLUMN "sentimentMagnitude" TO sentiment_magnitude

//set to the same value as the visitor id --> no way of setting a timestamp that made sense
ALTER TABLE analytics
DROP visit_start_time

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

DELETE FROM all_sessions WHERE visitor_id = 'fullVisitorId'


ALTER TABLE analytics
ALTER COLUMN date TYPE DATE USING TO_DATE(date, 'YYYYMMDD'),
ALTER COLUMN visit_number TYPE INT USING visit_number::INT,
ALTER COLUMN unit_price TYPE NUMERIC USING unit_price::NUMERIC, 
ALTER COLUMN units_sold TYPE SMALLINT USING units_sold::SMALLINT, 
ALTER COLUMN visit_id TYPE INT USING visit_id::INT, 
ALTER COLUMN page_views TYPE SMALLINT USING page_views::SMALLINT
ALTER COLUMN bounces TYPE SMALLINT USING bounces::SMALLINT
ALTER COLUMN revenue TYPE NUMERIC USING revenue::NUMERIC

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
	product_revenue = product_quantity * product_price
  
  UPDATE all_sessions
SET 
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
SET
	CASE 
		WHEN city = 'not available in demo dataset' OR city IS NULL 
		AND country IS NOT NULL
		THEN city = country
	ELSE city
END

SELECT item_quantity, item_revenue FROM all_sessions WHERE item_quantity IS NOT NULL OR item_revenue IS NOT NULL

ALTER TABLE all_sessions
DROP item_revenue,
DROP item_quantity,
DROP search_keyword,
DROP transaction_revenue //redundant --> returned same values as total_transaction_revenue

SET time = 
		CASE 
			WHEN time = '00:00:00' THEN NULL
			ELSE time
		END;

UPDATE all_sessions
SET product_revenue = ROUND(product_revenue/100000, 2)

// created a new table to separate product purchase data
DELETE FROM product_purchases
WHERE (product_sku, product_revenue) IN
(SELECT product_sku, product_revenue FROM product_purchases
GROUP BY product_sku, product_revenue
HAVING COUNT(*) > 1)

// adding primary keys and connecting the tables in the database
ALTER TABLE analytics ADD COLUMN visit_number SERIAL

ALTER TABLE analytics
ADD CONSTRAINT pk_analytics PRIMARY KEY(visit_id, visit_number)

ALTER TABLE all_sessions ADD COLUMN visit_number SERIAL

ALTER TABLE all_sessions
ADD CONSTRAINT pk_all_sessions PRIMARY KEY(visit_id, visit_number)

