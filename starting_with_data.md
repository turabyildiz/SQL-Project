Question 1: What is the first recorded order in the database? Include date along with the products sku.

SQL Queries:

SELECT als.date, productsku
FROM all_sessions als
JOIN analytics
USING(fullvisitorid)
GROUP BY als.date, productsku
ORDER BY als.date DESC

<!-- Answer: The oldest recorded order in the database is on August first, 2017. The product sku is different from the other ones, bieng an integer value. This is something that can be investigated further, perhaps this was a test order.  -->



Question 2: Output the orders in which the sentiment score was above 0.4 along with the name of the product ordered. Then, make a list of the average sentiment score across all orders with a date greater or equal to 7 years ago from today.

SQL Queries:


SELECT name, sentimentscore
FROM sales_report
WHERE sentimentscore > 0.4

<!-- Average: -->

SELECT AVG(sentimentscore) AS avg_sentiment_score, date
FROM sales_report
JOIN all_sessions
USING(productsku)
WHERE date >= '2017-03-02'
GROUP BY date
ORDER BY date


<!-- Answer: Sarting from 7 years ago today, i could not identify a pattern in the sentiment score overtime.  -->



Question 3: For each visit id, create a list of how many users were "not socially engaged" using that visit id.

SQL Queries:

SELECT visitid, COUNT(socialengagementtype) AS notengaged
FROM analytics
WHERE socialengagementtype = 'Not Socially Engaged' AND visitid IS NOT NULL
GROUP BY visitid
ORDER BY visitid


<!-- Answer: The output of this query is extensive. This shows just how many visit id's there really are. For each id, there is a varying amount of users that were not socially engaged.-->



