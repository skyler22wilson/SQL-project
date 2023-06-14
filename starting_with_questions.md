Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

--Elected to filter out columns where the city was = country since it was NULL prior and when the revenues werent 0 to provide a better picture of the data

SELECT city, country, SUM(total_transaction_revenue) total_revenue FROM all_sessions
WHERE city != country
GROUP BY city, country
HAVING SUM(total_transaction_revenue) > 0
ORDER BY total_revenue DESC


Answer: 






**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

//Average per city and country
SELECT a.country, a.city, ROUND(AVG(pd.product_quantity), 2) AS avg_quantity 
FROM all_sessions a
JOIN product_details pd ON pd.unique_id = a.unique_id
GROUP BY a.country, a.city
ORDER BY avg_quantity DESC

//Average with products ordered
SELECT a.country, a.city, ROUND(AVG(p.ordered_quantity), 2) AS avg_quantity_ordered 
FROM all_sessions a
JOIN product_details pd ON pd.unique_id = a.unique_id
JOIN products p ON p.sku = pd.product_sku
GROUP BY a.country, a.city
ORDER BY avg_quantity_ordered DESC

//Collective average
SELECT ROUND(AVG(product_quantity), 2) AS avg_quantity 
FROM product_details

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
//SUM of totala revenue by city and country
SELECT a.city, a.country, SUM(t.total_transaction_revenue)
FROM all_sessions a
JOIN transactions t ON t.transaction_id = a.transaction_id
GROUP BY a.city, a.country

//SUM of totala revenue by country
SELECT a.country, SUM(t.total_transaction_revenue)
FROM all_sessions a
JOIN transactions t ON t.transaction_id = a.transaction_id
GROUP BY a.country

//SUM of totala revenue by city
SELECT a.city, SUM(t.total_transaction_revenue)
FROM all_sessions a
JOIN transactions t ON t.transaction_id = a.transaction_id
GROUP BY a.city

//Summary statistics on total revenue
SELECT a.city, a.country, 
MAX(t.total_transaction_revenue) AS max_revenue, 
MIN(t.total_transaction_revenue) AS min_revenue,
ROUND(AVG(t.total_transaction_revenue), 2) AS avg_revenue
FROM all_sessions a
JOIN transactions t ON t.transaction_id = a.transaction_id
GROUP BY a.city, a.country


Answer:







