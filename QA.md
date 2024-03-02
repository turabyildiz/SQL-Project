What are your risk areas? Identify and describe them.

Some of the data may not be in a valid format. I also dont want to tamper with the original raw data too much. Not only is formatting an issue but so is readablity which affects the quality of the database.

1. The date doesnt seem to be in the correct format upon first glance.

2. As mentioned in the cleaning data file, the unit price is a huge number, making comparison difficult.

3. The values in column "fullvisitorid" which is in the "all_sessions" table are also massive numbers. I will fix this to give the database more quality.

4. visitstarttime in the analytics table seems off. it seems to be a clone of the visit id column. if this is an actual time, i will try to convert it.

5. The "name" column in the "sales_report" table has some spacing issues. Some of the names are fine, and some have an extra space before the name begins. If i fix this incorrectly, it may mess up any future attempts to work with the names, making it a risk area.

QA Process:
Describe your QA process and include the SQL queries used to execute it.

<!-- 1. I will check the format of the date, and limit it to 10 to speed up the process. -->

SELECT date
FROM all_sessions
LIMIT 10

The date seems to be in the correct format.

<!-- 2. I will alter the column unitprice in the table "all_sessions" to make things more comparable. -->

UPDATE analytics
SET unit_price = unit_price/1000000

Now the unit prices are much more comparable.

<!-- 3. I will divide the fullvisitorid by 10 to the power of 18 -->

UPDATE all_sessions
SET fullvisitorid = fullvisitorid/1000000000000000000

Now the ids are more readable and comparable when selecting them in any way. I will repeat this process for the analytics table as fullvisitorid is present there as well.

<!-- 4. i had to convert the data type here to double precision, as i was getting errors trying to make this a timestamp. -->

ALTER TABLE analytics
ALTER COLUMN visitstarttime SET DATA TYPE double precision

<!-- Then, I converted the number to the time, along with UTC as i was getting more errors. -->

ALTER TABLE analytics
ALTER COLUMN visitstarttime TYPE TIMESTAMP WITH TIME ZONE
USING to_timestamp(visitstarttime) AT TIME ZONE 'UTC'

<!-- 5. I will fix the spacing in the name column. -->

UPDATE sales_report
SET name = REPLACE(name, ' ', '')
WHERE name LIKE ' %' OR name LIKE ' '

<!-- Now, lets check on  the result -->

SELECT *
FROM sales_report
LIMIT 10
