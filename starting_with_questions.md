starting_with_data.Markdown

--**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


--SQL Queries:

--Elected to filter out columns where the city was = country since it was NULL prior and when the revenues werent 0 to provide a better picture of the data

SELECT city, country, SUM(total_transaction_revenue) total_revenue,
RANK() OVER (ORDER BY SUM(total_transaction_revenue) DESC )
FROM all_sessions
WHERE city != country
GROUP BY city, country
HAVING SUM(total_transaction_revenue) > 0
LIMIT 10;

--Answer: from the query below you can see that San Francisco has the highest transaction value

| "city"          | "country"       | "total_revenue" | "rank" |
|-----------------|-----------------|-----------------|--------|
| "San Francisco" | "United States" | "15643.20"      | "1"    |
| "Sunnyvale"     | "United States" | "9922.30"       | "2"    |
| "Atlanta"       | "United States" | "8544.40"       | "3"    |
| "Palo Alto"     | "United States" | "6080.00"       | "4"    |
| "Tel Aviv-Yafo" | "Israel"        | "6020.00"       | "5"    |
| "New York"      | "United States" | "5303.60"       | "6"    |
| "Mountain View" | "United States" | "4833.60"       | "7"    |
| "Los Angeles"   | "United States" | "4794.80"       | "8"    |
| "Chicago"       | "United States" | "4495.20"       | "9"    |
| "Sydney"        | "Australia"     | "3580.00"       | "10"   |

--**Question 2: What is the average number of products ordered from visitors in each city and country?**


--SQL Queries: 

--Average per city and country
SELECT a.country, a.city, ROUND(AVG(pd.product_quantity), 2) AS avg_quantity 
FROM all_sessions a
JOIN product_details pd ON pd.unique_id = a.unique_id
GROUP BY a.country, a.city
ORDER BY avg_quantity DESC

--Answer: Bouler has the highest average quantity ordered on the site


| "country"       | "city"        | "avg_quantity" |
|-----------------|---------------|----------------|
| "United States" | "Boulder"     | "6.25"         |
| "Mexico"        | "Mexico"      | "0.96"         |
| "United States" | "Las Vegas"   | "0.33"         |
| "Philippines"   | "Makati"      | "0.33"         |
| "United States" | "San Mateo"   | "0.33"         |
| "Canada"        | "Quebec City" | "0.14"         |
| "China"         | "China"       | "0.12"         |
| "Russia"        | "Moscow"      | "0.11"         |
| "United States" | "Cupertino"   | "0.08"         |
| "Hungary"       | "Hungary"     | "0.04"         |

--**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


--SQL Queries:

SELECT subq.city, subq.country, subq.product_category, subq.quantity_count
--subquery to only select city, country and category where a transaction occured
FROM (
	SELECT a.city, a.country, pd.product_category,
	COUNT(pd.product_quantity) AS quantity_count, pd.product_quantity
	FROM all_sessions a
	JOIN product_details pd ON a.unique_id = pd.unique_id
	-- filtered out cities where they were NULL because the data provided was not really informative
    WHERE pd.product_quantity > 0 AND a.city != a.country AND pd.product_category IS NOT NULL
	GROUP BY a.country, a.city, pd.product_category, pd.product_quantity
	ORDER BY a.country 
) AS subq

--Answer:
| "city"          | "country"       | "product_category"                    | "quantity_count" |
|-----------------|-----------------|---------------------------------------|------------------|
| "Melbourne"     | "Australia"     | "Lifestyle"                           | "1"              |
| "Quebec City"   | "Canada"        | "Home/Apparel/Men's/Men's-T-Shirts/"  | "1"              |
| "New Delhi"     | "India"         | "Apparel"                             | "1"              |
| "Mexico City"   | "Mexico"        | "Home/Nest/Nest-USA/"                 | "1"              |
| "Makati"        | "Philippines"   | "Electronics"                         | "1"              |
| "Moscow"        | "Russia"        | "${escCatTitle}"                      | "1"              |
| "Moscow"        | "Russia"        | "Drinkware"                           | "1"              |
| "Boulder"       | "United States" | "Bags"                                | "1"              |
| "Cupertino"     | "United States" | "Home/Apparel/Headgear/"              | "1"              |
| "Kirkland"      | "United States" | "Headgear"                            | "1"              |
| "Las Vegas"     | "United States" | "Nest-USA"                            | "1"              |
| "Mountain View" | "United States" | "Home/Nest/Nest-USA/"                 | "2"              |
| "Mountain View" | "United States" | "Nest-USA"                            | "1"              |
| "New York"      | "United States" | "Home/Apparel/Men's/Men's-Outerwear/" | "1"              |
| "New York"      | "United States" | "Home/Nest/Nest-USA/"                 | "1"              |
| "San Francisco" | "United States" | "${escCatTitle}"                      | "1"              |
| "San Jose"      | "United States" | "Nest-USA"                            | "1"              |
| "San Mateo"     | "United States" | "${escCatTitle}"                      | "1"              |
| "Sunnyvale"     | "United States" | "Home/Nest/Nest-USA/"                 | "1"              |
| "Sunnyvale"     | "United States" | "Home/Shop by Brand/YouTube/"         | "1"  

--Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


--SQL Queries:

SELECT city, country, product_name, product_quantity
FROM (
    SELECT a.city, a.country, p.product_name, p.product_quantity, 
        ROW_NUMBER() OVER (PARTITION BY a.city, a.country ORDER BY p.product_quantity DESC) AS product_rank
    FROM product_details p
	JOIN all_sessions a ON a.unique_id = p.unique_id
    -- filtered out where the city was NULL and assigned as the country name
    WHERE product_quantity > 0 AND city != country
) ranked_products
WHERE product_rank = 1
LIMIT 25;

--Answer:
| "city"          | "country"       | "product_name"                                            | "product_quantity" |
|-----------------|-----------------|-----------------------------------------------------------|--------------------|
| "Boulder"       | "United States" | "Reusable Shopping Bag"                                   | 50                 |
| "Cupertino"     | "United States" | "Google Twill Cap"                                        | 1                  |
| "Kirkland"      | "United States" | "Android Wool Heather Cap Heather/Black"                  | 1                  |
| "Las Vegas"     | "United States" | "Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel" | 1                  |
| "Makati"        | "Philippines"   | "Compact Bluetooth Speaker"                               | 1                  |
| "Melbourne"     | "Australia"     | "Google Sunglasses"                                       | 1                  |
| "Mexico City"   | "Mexico"        | "Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel" | 1                  |
| "Moscow"        | "Russia"        | "Google 22 oz Water Bottle"                               | 1                  |
| "Mountain View" | "United States" | "Red Spiral Google Notebook"                              | 8                  |
| "New Delhi"     | "India"         | "Google Men's Vintage Badge Tee Black"                    | 1                  |
| "New York"      | "United States" | "Google Men's Pullover Hoodie Grey"                       | 1                  |
| "Quebec City"   | "Canada"        | "YouTube Men's Short Sleeve Hero Tee Black"               | 1                  |
| "San Francisco" | "United States" | "Google Laptop and Cell Phone Stickers"                   | 1                  |
| "San Jose"      | "United States" | "Nest® Cam Indoor Security Camera - USA"                  | 1                  |
| "San Mateo"     | "United States" | "Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel" | 1                  |
| "Sunnyvale"     | "United States" | "YouTube Twill Cap"                                       | 1                  |

--Question 5: Can we summarize the impact of revenue generated from each city/country?**

--SQL Queries:
-- elected to use summary stats to provide additional insight
SELECT city, country, 
	   SUM(total_transaction_revenue) AS total_revenue,
	   MIN(total_transaction_revenue) AS min_revenue,
	   MAX(total_transaction_revenue) AS max_revenue,
	   ROUND(AVG(total_transaction_revenue), 2) AS avg_revenue
FROM all_sessions
WHERE city != country -- same as logic above, not informative when city name == coountry name
GROUP BY city, country
-- filtered out any cities and countries with no transaction revenue
HAVING SUM(total_transaction_revenue) > 0 AND
	   AVG(total_transaction_revenue) > 0
ORDER BY country

--Answer:
| "city"          | "country"       | "total_revenue" | "min_revenue" | "max_revenue" | "avg_revenue" |
|-----------------|-----------------|-----------------|---------------|---------------|---------------|
| "Sydney"        | "Australia"     | "3580.00"       | "0.00"        | "3580.00"     | "48.38"       |
| "New York"      | "Canada"        | "679.90"        | "679.90"      | "679.90"      | "679.90"      |
| "Toronto"       | "Canada"        | "821.60"        | "0.00"        | "821.60"      | "6.18"        |
| "Tel Aviv-Yafo" | "Israel"        | "6020.00"       | "0.00"        | "6020.00"     | "128.09"      |
| "Zurich"        | "Switzerland"   | "169.90"        | "0.00"        | "169.90"      | "7.08"        |
| "Mountain View" | "United States" | "4833.60"       | "0.00"        | "1560.00"     | "4.11"        |
| "San Francisco" | "United States" | "15643.20"      | "0.00"        | "3010.00"     | "33.86"       |
| "Austin"        | "United States" | "1577.80"       | "0.00"        | "1220.00"     | "14.61"       |
| "Houston"       | "United States" | "389.80"        | "0.00"        | "389.80"      | "7.09"        |
| "San Bruno"     | "United States" | "1037.70"       | "0.00"        | "1037.70"     | "22.08"       |
| "Seattle"       | "United States" | "3580.00"       | "0.00"        | "3580.00"     | "29.83"       |
| "New York"      | "United States" | "5303.60"       | "0.00"        | "1520.00"     | "8.21"        |
| "Nashville"     | "United States" | "1570.00"       | "1570.00"     | "1570.00"     | "1570.00"     |
| "Los Angeles"   | "United States" | "4794.80"       | "0.00"        | "3630.00"     | "21.70"       |
| "Atlanta"       | "United States" | "8544.40"       | "0.00"        | "7424.80"     | "129.46"      |
| "San Jose"      | "United States" | "2623.80"       | "0.00"        | "1540.00"     | "10.62"       |
| "Palo Alto"     | "United States" | "6080.00"       | "0.00"        | "3050.00"     | "62.68"       |
| "Sunnyvale"     | "United States" | "9922.30"       | "0.00"        | "6492.40"     | "27.11"       |
| "Chicago"       | "United States" | "4495.20"       | "0.00"        | "3060.00"     | "22.59"       |
| "Columbus"      | "United States" | "219.90"        | "0.00"        | "219.90"      | "109.95"      |





