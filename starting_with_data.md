--Question 1: what can the most overall most popular and least popular pages and the summary statistics of the number of pages looked at for each type (page_title) tell us about different city/country preferences?

SQL Queries:

//Most popular
SELECT RANK() OVER (ORDER BY MAX(page_views) DESC) AS ranking,
	   MIN(page_views) AS min_views, MAX(page_views) AS most_views, ROUND(AVG(page_views), 2) AS avg_views,
    city, country, page_title
FROM all_sessions
-- not informative to have city name be the country name which was assigned if city name was NULL

WHERE city != country
GROUP BY page_title, city, country
LIMIT 10;

Answer:

--DISCLAIMER: slide over to see the rest of the table


| "ranking" | "min_views" | "most_views" | "avg_views" | "city"          | "country"        | "page_title"                                                |
| --------- | ----------- | ------------ | ----------- | --------------- | ---------------- | ----------------------------------------------------------- |
| "1"       | 30          | 30           | "30.00"     | "Jersey City"   | "United States"  | "Backpacks / Bags / Google Merchandise Store"               |
| "2"       | 4           | 29           | "12.60"     | "Mountain View" | "United States"  | "Power & Chargers / Electronics / Google Merchandise Store" |
| "3"       | 26          | 26           | "26.00"     | "Bogota"        | "Colombia"       | "Nest-USA"                                                  |
| "4"       | 2           | 24           | "8.78"      | "Sunnyvale"     | "United States"  | "Accessories / Google Merchandise Store"                    |
| "5"       | 1           | 23           | "5.00"      | "London"        | "United Kingdom" | "Nest-USA"                                                  |
| "6"       | 1           | 22           | "6.45"      | "San Francisco" | "United States"  | "Nest-USA"                                                  |
| "7"       | 1           | 21           | "6.17"      | "San Bruno"     | "United States"  | "YouTube / Shop by Brand / Google Merchandise Store"        |
| "7"       | 4           | 21           | "11.50"     | "Minato"        | "Japan"          | "Fun / Accessories / Google Merchandise Store"              |
| "7"       | 21          | 21           | "21.00"     | "Montreal"      | "Canada"         | "Audio / Electronics / Google Merchandise Store"            |
| "7"       | 3           | 21           | "7.83"      | "Sunnyvale"     | "United States"  | "Google"                                                    |


Question 3: Which cities/countries stay on the page for the longest time, what pages did they view and how many?

SQL Queries:
SELECT city, country, page_title, page_views,
MAX(time_on_site) AS most_time_spent
FROM all_sessions

-- not informative to have city name be the country name which was assigned if city name was NULL
WHERE country != city
GROUP BY city, country, page_title, page_views
HAVING MAX(time_on_site) IS NOT NULL AND MAX(time_on_site) IS NOT NULL
ORDER BY most_time_spent DESC

Answer:


| "city"          | "country"       | "page_title"                                                | "page_views" | "most_time_spent" |
| --------------- | --------------- | ----------------------------------------------------------- | ------------ | ----------------- |
| "San Bruno"     | "United States" | "Sports & Fitness / Accessories / Google Merchandise Store" | 11           | "00:23:00"        |
| "Tel Aviv-Yafo" | "Israel"        | "Men's"                                                     | 5            | "00:23:00"        |
| "Mountain View" | "United States" | "Men's"                                                     | 7            | "00:23:00"        |
| "San Francisco" | "United States" | "Women's"                                                   | 4            | "00:23:00"        |
| "Lake Oswego"   | "United States" | "Accessories / Google Merchandise Store"                    | 13           | "00:23:00"        |
| "Mountain View" | "United States" | "Drinkware"                                                 | 2            | "00:23:00"        |
| "Mountain View" | "United States" | "Drinkware"                                                 | 3            | "00:23:00"        |
| "Mountain View" | "United States" | "Apparel / Google Merchandise Store"                        | 3            | "00:23:00"        |
| "Kiev"          | "Ukraine"       | "Electronics / Google Merchandise Store"                    | 4            | "00:23:00"        |
| "Houston"       | "United States" | "Accessories / Google Merchandise Store"                    | 6            | "00:23:00"        |




Question 3: How does the amount of time spent inform us about peoples purchasing habits on the website?


SELECT country, city, page_views,
	   SUM(total_transaction_revenue) AS total_revenue,
	   ROUND(AVG(total_transaction_revenue), 2) AS avg_revenue
FROM all_sessions

-- not informative to have city name be the country name which was assigned if city name was NULL
WHERE city != country
GROUP BY country, city, page_views
HAVING SUM(total_transaction_revenue)> 0 AND AVG(total_transaction_revenue) > 0
ORDER BY page_views DESC
LIMIT 20;

Answer:


| "country"       | "city"          | "page_views" | "total_revenue" | "avg_revenue" |
| --------------- | --------------- | ------------ | --------------- | ------------- |
| "United States" | "San Francisco" | 22           | "1960.90"       | "1960.90"     |
| "United States" | "San Jose"      | 20           | "1540.00"       | "1540.00"     |
| "United States" | "Atlanta"       | 20           | "7424.80"       | "7424.80"     |
| "United States" | "New York"      | 19           | "579.90"        | "289.95"      |
| "United States" | "San Francisco" | 19           | "619.70"        | "619.70"      |
| "United States" | "Houston"       | 18           | "389.80"        | "389.80"      |
| "United States" | "Mountain View" | 18           | "133.90"        | "19.13"       |
| "United States" | "San Francisco" | 18           | "121.90"        | "60.95"       |
| "Canada"        | "Toronto"       | 18           | "821.60"        | "821.60"      |
| "United States" | "Austin"        | 18           | "357.80"        | "357.80"      |
| "United States" | "Austin"        | 17           | "1220.00"       | "1220.00"     |
| "United States" | "New York"      | 17           | "819.60"        | "204.90"      |
| "United States" | "San Francisco" | 17           | "2810.00"       | "562.00"      |
| "United States" | "Atlanta"       | 17           | "1119.60"       | "559.80"      |
| "United States" | "Los Angeles"   | 16           | "1164.80"       | "291.20"      |
| "Canada"        | "New York"      | 16           | "679.90"        | "679.90"      |
| "United States" | "San Francisco" | 16           | "3010.00"       | "1003.33"     |
| "United States" | "New York"      | 16           | "1270.00"       | "317.50"      |
| "United States" | "Mountain View" | 16           | "1560.00"       | "130.00"      |
| "United States" | "San Francisco" | 15           | "3415.10"       | "853.78"      |
| "United States" | "Sunnyvale"     | 15           | "1429.90"       | "238.32"      |
| "United States" | "Seattle"       | 15           | "3580.00"       | "3580.00"     |
| "United States" | "San Jose"      | 15           | "1083.80"       | "1083.80"     |
| "United States" | "Mountain View" | 15           | "89.80"         | "11.23"       |
| "Israel"        | "Tel Aviv-Yafo" | 14           | "6020.00"       | "6020.00"     |
| "United States" | "San Francisco" | 14           | "1394.20"       | "199.17"      |
| "United States" | "Columbus"      | 14           | "219.90"        | "219.90"      |
| "United States" | "Mountain View" | 14           | "359.90"        | "35.99"       |
| "United States" | "San Bruno"     | 14           | "1037.70"       | "518.85"      |
| "Switzerland"   | "Zurich"        | 14           | "169.90"        | "169.90"      |
| "United States" | "New York"      | 14           | "1520.00"       | "190.00"      |
| "United States" | "Mountain View" | 13           | "711.90"        | "33.90"       |
| "United States" | "Palo Alto"     | 13           | "3050.00"       | "1525.00"     |
| "United States" | "Chicago"       | 13           | "195.80"        | "65.27"       |
| "United States" | "Los Angeles"   | 13           | "3630.00"       | "1210.00"     |
| "United States" | "Mountain View" | 12           | "1808.20"       | "100.46"      |
| "United States" | "San Francisco" | 12           | "1230.00"       | "410.00"      |
| "United States" | "New York"      | 12           | "513.30"        | "171.10"      |
| "United States" | "Palo Alto"     | 11           | "1510.00"       | "755.00"      |
| "United States" | "Nashville"     | 11           | "1570.00"       | "1570.00"     |
| "United States" | "San Francisco" | 11           | "1081.40"       | "154.49"      |
| "United States" | "New York"      | 11           | "600.80"        | "33.38"       |
| "United States" | "Chicago"       | 10           | "3060.00"       | "510.00"      |
| "United States" | "Mountain View" | 10           | "169.90"        | "7.08"        |
| "United States" | "Palo Alto"     | 10           | "1520.00"       | "760.00"      |
| "United States" | "Sunnyvale"     | 6            | "8492.40"       | "207.13"      |
| "Australia"     | "Sydney"        | 5            | "3580.00"       | "596.67"      |
| "United States" | "Chicago"       | 5            | "1239.40"       | "45.90"       |
