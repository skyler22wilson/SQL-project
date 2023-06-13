Question 1:how does the average quantity ordered inform us on a countries/city's consumption habits

SQL Queries: 
SELECT a.country, a.city, ROUND(AVG(p.ordered_quantity), 2) AS avg_quantity_ordered 
FROM all_sessions a
JOIN product_details pd ON pd.unique_id = a.unique_id
JOIN products p ON p.sku = pd.product_sku
GROUP BY a.country, a.city
ORDER BY avg_quantity_ordered DESC


Answer: 



Question 2: what can the most overall most popular and least popular pages and the summary statistics of the number of pages looked at for each type (page_title) tell us about different country preferences?

SQL Queries:

//Most popular
SELECT MAX(page_views) AS most_views, page_title
FROM all_sessions
GROUP BY page_title
ORDER BY most_views DESC

//Least Popular
SELECT MIN(page_views) AS most_views, page_title
FROM all_sessions
GROUP BY page_title
ORDER BY most_views 

SELECT country, page_title, MAX(page_views), MIN(page_views), AVG(page_views) AS avg_views
FROM all_sessions 
GROUP BY page_title, country


Answer:


Question 3: How does the amount of time spent inform us about peoples purchasing habits on the website?

--How does the amount of time spent inform us about country's purchasing habits on the website?
SELECT DISTINCT(a.country), a.city, a.page_views, t.total_transaction_revenue
FROM all_sessions a
JOIN transactions t ON t.transaction_id = a.transaction_id
ORDER BY a.page_views DESC

SQL Queries:

Answer:



Question 4: Which cities/countries stay on the page for the longest time, what pages did they view and how many?

SQL Queries:
SELECT city, country, page_title, page_views, MAX(time_on_site) AS most_time_spent 
FROM all_sessions
WHERE country IS NOT NULL 
GROUP BY city, country, page_title, page_views
HAVING MAX(time_on_site) IS NOT NULL AND MAX(time_on_site) NOT LIKE '00:00:00'
ORDER BY most_time_spent DESC

Answer:



Question 5: How does the average restocking leadtime for each product affect how much products are ordered and what the consumer sentiment was for each product in each country

SQL Queries: 

SELECT p.sku, pd.product_name, SUM(p.ordered_quantity) AS total_ordered_quantity, 
	   AVG(p.restocking_lead_time) AS avg_restock_lead_time, pd.country
FROM product_details pd
JOIN products p ON p.sku = pd.product_sku
JOIN sales_report s ON p.sku = s.product_sku
GROUP BY p.sku, pd.country, pd.product_name
ORDER BY avg_restock_lead_time DESC

Answer:
