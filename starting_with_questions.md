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



Answer:





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







