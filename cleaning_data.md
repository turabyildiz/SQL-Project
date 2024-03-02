What issues will you address by cleaning the data?

1. I will divide the unit price by one million to make the price more readable

2. I will be cleaning the empty values in the city column, removing them from the dataset as there is no way to guess which city is to be in place of "not available in demo dataset" or "(not set)". For the first question, any null value in "time on site" will actually help me identify which city/country to not consider when evaluating time on site revenue. I will however keep these values in the actual dataset as i do not want to tamper with the raw data.

3. I will clean the "(not set)" and "${escCatTitle}" values so they dont interfere with finding the category patterns. Again, i will not tamper too much with the raw data as it is risky.

4. I would like to remove all completley empty columns from the database without editing raw data. Specifically from the "analytics" table.

Queries:
Below, provide the SQL queries you used to clean your data.

1. UPDATE analytics
   SET unit_price = unit_price/1000000

2. CASE
   WHEN city = 'not available in demo dataset' THEN country
   WHEN city = '(not set)' THEN country
   ELSE city

3. WHEN v2productcategory = '(not set)' THEN 'misc'
   WHEN v2productcategory = '${escCatTitle}' THEN 'misc'
   ELSE v2productcategory

<!-- 4. Lets make a view for this problem: -->

CREATE VIEW analytics_v2 AS
SELECT
visitnumber,
visitid,
visitstarttime,
date,
fullvisitorid,
channelgrouping,
socialengagementtype,
pageviews,
timeonsite,
unit_price
FROM analytics

<!-- Now we can select the view via its name and have a cleaner dataset. -->

