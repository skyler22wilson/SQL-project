What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.

//check if the visit_id is unique
SELECT visit_id, COUNT(*) AS count 
FROM analytics 
GROUP BY visit_id
HAVING COUNT(*) > 1
