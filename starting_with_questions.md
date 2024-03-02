Answer the following questions and provide the SQL queries used to find the answer.

**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

SQL Queries:

SELECT
CASE
WHEN city = 'not available in demo dataset' THEN country
WHEN city = '(not set)' THEN country
ELSE city
END AS city, country, timeonsite, MAX(productprice * total_ordered) AS total_rev, productsku
FROM all_sessions
JOIN sales_by_sku USING (productsku)
GROUP BY city, country, timeonsite, productsku
ORDER BY total_rev DESC

<!-- Answer: The cities with most transaction revenue on site are Chicago, Mountain View, San Francisco, London, etc. The countries with the most transaction revenue on site are the U.S and U.K countries. -->

**Question 2: What is the average number of products ordered from visitors in each city and country?**

SQL Queries:

SELECT
CASE
WHEN city = 'not available in demo dataset' THEN country
WHEN city = '(not set)' THEN country
ELSE city
END AS city, country, AVG(total_ordered) AS avg_prod_ordered
FROM all_sessions
JOIN sales_by_sku
USING(productsku)
GROUP BY city, country
ORDER BY avg_prod_ordered DESC

<!-- Answer: Saudi Arabia, Czechia, and U.S had the most average products ordered. -->

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

SQL Queries:

<!-- This query finds the result by location -->

SELECT country,
CASE
WHEN v2productcategory = '(not set)' THEN 'misc'
WHEN v2productcategory = '${escCatTitle}' THEN 'misc'
ELSE v2productcategory
END AS category, COUNT(*) AS total_ordered
FROM all_sessions
GROUP BY country, category
ORDER BY total_ordered DESC

<!-- This query finds the total overall order count by category -->

SELECT
CASE
WHEN v2productcategory = '(not set)' THEN 'misc'
WHEN v2productcategory = '${escCatTitle}' THEN 'misc'
ELSE v2productcategory
END AS category,
SUM(1) AS order_count
FROM all_sessions
GROUP BY category
ORDER BY order_count DESC

<!-- Answer: As seen in the outputs, the U.S has many orders across many categories of products. The most ordered category for the U.S would be in the "Home/Apparel/Men's/Men's-T-Shirts/" category. However, the most ordered category across all products is "Home/Shop by Brand/YouTube/". -->

**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

SQL Queries:

WITH top_products AS (
SELECT city, country, v2productname,
COUNT(_) AS order_number,
ROW_NUMBER() OVER (PARTITION BY city, country ORDER BY COUNT(_) DESC) AS rank
FROM all_sessions
GROUP BY city, country, v2productname
)

SELECT city, country, v2productname AS top_product_name, order_number,
CASE
WHEN city = 'not available in demo dataset' THEN country
WHEN city = '(not set)' THEN country
ELSE city
END AS city
FROM top_products
ORDER BY order_number DESC

Answer: I had to create a CTE with a window function here to get the most ordered per location. After this, i made it so that if the city is unavailable, replace the value with the country.

There are varying answers. In the U.S, the top hit is "Google Men's 100% Cotton Short Sleeve Hero Tee White" with 84 sales. In the U.K, it is "YouTube Hard Cover Journal" with 21 sales. The pattern seems to be that most orders in general are actually from the U.S.

**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries: 

<!-- This query groups sales by country -->

SELECT country, SUM(productprice * total_ordered) AS total_sales
FROM all_sessions
JOIN sales_report USING (productsku)
WHERE country IS NOT NULL AND country != 'not available in demo dataset' AND country != '(not set)'
GROUP BY country
ORDER BY total_sales DESC

<!-- This query groups sales by city -->

SELECT city, SUM(productprice * total_ordered) AS total_sales
FROM all_sessions
JOIN sales_report USING (productsku)
WHERE city IS NOT NULL AND country != 'not available in demo dataset' AND country != '(not set)'
GROUP BY city
ORDER BY total_sales DESC


Answer: In the outputs, it is seen that the U.S had been impacted most by revenue generated as they come out on top by a significant amount. as expected from my previous querys, the U.K comes in second place, then Canada, India, Italy etc.

As for the individual cities, the top number one city is not avaliable in the dataset. We cannot actually know the location as the country cannot replace the city value here. In this dataset there is no city and country for this value. Following this value we have more U.S cities like Mountain View and San Francisco. It can be safely assumed however, that the number one sales generator is a U.S city.
