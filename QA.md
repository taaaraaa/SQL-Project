# Risk Areas
What are your risk areas? Identify and describe them.
- One of the major risk areas is the deletion of the huge amount of null data. What if these data were important?! For example, due to lack of information, I assumed that all currency units are in USD, but it might not be 100% accurate. According to the [cleaning_data file](/cleaning_data.md), we cleaned following data:
	- 5 columns in sales_report table
	- 16 columns in all_sessions table
	- 4 column of analytics table
	- Deleting sales_by_sku table

- Another risk area is making sure that in future the columns that were deleted won't be needed and won't be updated.


# QA Process:
Describe your QA process and include the SQL queries used to execute it.

For QA I wanted to make sure that my data are:
1. Complete (no blank or null values)
2. Unique (no duplicate values)
3. Consistent

## 1. Are my data complete?
Apart from recognizing many null columns in [cleaning_data](/cleaning_data.md) step, in the QA process, I realized that there are even more imcomplete columns in the data base. For example, in analytics table there are very limited rows that contain information about units_sold. I run the following quary to get the number of null rows, total number of rows and percentage of null values in this column. We have 96% missing values! 

``` SQL
SELECT 
-- First Column
	COUNT(*) AS Namber_of_Nulls_UnitsSold ,
-- Second Column
	(SELECT
	 		count(*) AS Number_Of_Rows
	 		FROM analytics),
-- Third Column
	 CAST(COUNT(*) AS decimal)/
	 (SELECT
	 		count(*)
	 		FROM analytics) 
	AS Percentage_of_Null 
	
	FROM analytics
	where units_sold is Null;
```
Look at the results:

![Units_sold column information](/data/Units_sold_column.png)

In this table, analytics, I also realized that, although not null, there are no values in  socialengagementtype column. So this column was dropped!

``` SQL
SELECT
	 COUNT(*),
	(SELECT COUNT(*) From analytics)
From analytics
	WHERE socialengagementtype =('Not Socially Engaged');
```
![socialengagementtype](/data/socialengagementtype.png)

Another table that I identified with missing important value, was "all_sessions table"! In this table also, we had some missing values! Although, it was not significant, but because it is about price and revenue, even small amount counts!

``` SQL
SELECT
   COUNT (*) AS ZERO_Priced_Products,
   (SELECT
		COUNT(*) AS Number_Of_Rows
		FROM all_sessions)
FROM
  all_sessions
  where productprice=0;
```
![missing price information](/data/productprice_missing_values.png)
## 2. Are my data Unique?
This step was accurately done by defining Primary keys for each table. Repetitive data was recognized and removed in [cleaning_data](/cleaning_data.md) step. An overview of the tables, primary keys and their relationship can be seen in the ERD diagram in [Readme section](/README.md). 

## 3. Are my data Consistent?

First of all, I realized that had made a mistake in creating tables in SQL. Data type of units_sold should be integer, but it was VARCHAR, so by the following code, it was revised.

``` SQL
ALTER TABLE analytics
ALTER COLUMN units_sold TYPE INT
USING units_sold::integer;
```

Also, in order to check that countries and cities in all_sessions table are consistent with each other, an aggregate function was used to see the results. It seemed that data was consistent, but further investigation is required.

``` SQL
SELECT 
	country,city 
FROM all_sessions
	GROUP BY country,city
	ORDER BY country;

```
