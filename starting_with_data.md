Question 1: What are the most popular pages on the website, least popular?

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

Answer: 



Question 2: what can the summary statistics of the number of pages looked at for each type (page_title) tell us about 
different country preferences?

SQL Queries:

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



Question 5: 

SQL Queries:

Answer:
