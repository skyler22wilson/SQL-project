README.Markdown

# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
My primary goal of this project is to gain a better understanding of the entire process of data science/data analytics from learning how to use the SQL language to clean a messy database, ensure that the adequate QA standards are met and to run cqueries the provide useful or insightful information about the database being used

## Process
Step 1: upload the CSV  and create the ecommerce database
Step 2: I poked around the database to see what information was provided and what data needed to be cleaned
        I also explored how the data may be connected and which tables had similar or the same data columns in them
Step 3: I altered the data types to their proper type since i uploaded the CSV files with all columns having a VARCHAR type
Step 4: I made any neccesary alterations to reformat text, get numbers to a value that made sense, rounded values and adjustred time if the time did not make sense
Step 5: I connected the databases with primary and foreign keys where possible and created a new table that pertained just to product details
Step 6: to connect the product_details table to all_sessions i generated unique keys for both which allowed me to access both tables with a JOIN so I could easily complete the questions and it made the data cleaner
Step 7: I removed any columns that were either NULL or redundant
Step 8: I began my QA process where i checked for uniqueness, completeness and accuracy
Step 9: I discovered that the ecommerce labled tables were related so I filled in NULL values 
Step 10: filled in NULL values where applicable

## Results
Based on the questions provided by Lighthouse Labs and the questions I thought of on my own I was able to discover the consumption habits of people from all over the world that used the ecommerce website, how long they used the site for and how many pages they viewed. I used page views and site length to further analyze consumption habits because I wanted to see if engagement lead to more purchases.

## Challenges 
The primary challenge was most definitly cleaning and re-organizig the database. It was difficult to grasp a clear picture of what the data was offering because of how diorganized it was which made the initial process of cleaning it even harder because i could not figure out what the data was trying to tell me. 

Another challenge I faced was coming up with a valid QA plan that was both pheasible given my SQL expertice and effective in ensuring the quality of the data. 

On a more conceptual note, I think for me personally, this project waas difficult because I did not know or understand where the data came from. I think if I knew how it was generated I would have been able to clean the data and organize and normalize the database much better than I did.

## Future Goals
I would definitely like to completely normalize the database so that I could run my queries and have 100% confidence that the results from the data are verifiable and make sense. I would have also liked to analyze how all the data connnects and where it came from to provide more context.