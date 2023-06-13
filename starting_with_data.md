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


Question 3: Calculate the average amount of time spent on the website country, and the average number of bounces

SQL Queries:

Answer:



Question 4: How does the average amount of time spent and the average amount of bounces
inform us about peoples purchasing habits on the website?

SQL Queries:

Answer:



Question 5: Which cities/countries stay on the page for the longest time, what pages did they view and how many?

SQL Queries:

Answer:
