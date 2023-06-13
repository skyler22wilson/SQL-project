Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
SELECT a.country, a.city, t.total_transaction_revenue FROM all_sessions AS a
JOIN transactions AS t ON a.transaction_id = t.transaction_id
ORDER BY t.total_transaction_revenue DESC


Answer: 
Countries:                      City:
United States: $10154.80        Sunnyvale: $6492.40. **DISCLAIMER --> city name for top USA was NULL
Australia: $3580.00             Sydney: $3580.00





**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

//Average per city and country
SELECT country, city, ROUND(AVG(product_quantity), 2) AS avg_quantity FROM all_sessions
GROUP BY country, city
ORDER BY avg_quantity DESC

//Collective average
AS total_avg

Answer: 0.01


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
//country
SELECT country, product_category, COUNT(*)
FROM product_details
GROUP BY product_category, country
ORDER BY country

//city <-- less informative w/o country name but allows you to analyze each city's consumption habits
SELECT city, product_category, COUNT(*)
FROM product_details
GROUP BY product_category, city
ORDER BY city

//both city and country
SELECT city, country, product_category, COUNT(*)
FROM product_details
GROUP BY product_category, city, country
ORDER BY city, country


Answer: 
    
  

**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

//top product for each country
SELECT country, product_name, product_quantity
FROM (
    SELECT city, country, product_name, product_quantity, 
        ROW_NUMBER() OVER (PARTITION BY country ORDER BY product_quantity DESC) AS product_rank
    FROM product_details
    WHERE product_quantity > 0
) ranked_products
WHERE product_rank = 1;

//top product for each city
SELECT city, product_name, product_quantity
FROM (
    SELECT city, country, product_name, product_quantity, 
        ROW_NUMBER() OVER (PARTITION BY city ORDER BY product_quantity DESC) AS product_rank
    FROM product_details
    WHERE product_quantity > 0
) ranked_products
WHERE product_rank = 1;

// Both city and country
SELECT city, country, product_name, product_quantity
FROM (
    SELECT city, country, product_name, product_quantity, 
        ROW_NUMBER() OVER (PARTITION BY city, country ORDER BY product_quantity DESC) AS product_rank
    FROM product_details
    WHERE product_quantity > 0
) ranked_products
WHERE product_rank = 1;



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:




Answer:







