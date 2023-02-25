# What issues will you address by cleaning the data?
1. [Removing duplicate rows](#1-removing-duplicate-rows)
2. [Deleting repetitive columns](#2-deleting-repetitive-columns)
3. [Deleting sales_by_sku table](#3-deleting-sales_by_sku-table)
4. [Deleting Nall value coloumns](#4-deleting-null-columns)
5. [Making big numbers smaller](#5making-big-numbers-smaller)
6. [Rename a Column Name](#6rename-a-column-name)
7. [Deleting Useless columns](#7-deleting-useless-columns)


# Below, provide the SQL queries you used to clean your data.

## 1. Removing duplicate rows
Only the first 2 tables have duplicate rows.
```SQL
CREATE TABLE all_sessions_tmp AS
    Select DISTINCT * from all_sessions;

DROP TABLE all_sessions;

ALTER TABLE all_sessions_tmp
    RENAME TO all_sessions;
```
```SQL
CREATE TABLE analytics_tmp AS
	Select DISTINCT * from analytics;

DROP TABLE analytics;

ALTER TABLE analytics_tmp
	RENAME TO analytics;
```
## 2. Deleting repetitive columns
- ### Sales_report Table
First of all sales_report table has only ***454 rows*** and products table has ***1092 rows***. By joining the sales_report table and products table on *"productSKU"* and *"SKU"*, it results in 454 rows with repetitive data. So it becomes clear that we need to remove duplicate columns in **Sales_report**. 

```sQL
SELECT COUNT(productSKU)
FROM sales_report;
```
```sQL
SELECT COUNT(sku)
FROM products;
```
``` SQL
SELECT 
	A.productSKU,
	B.sku,
	A.name,
	B.name,
	A.stockLevel,
	B.stockLevel,
	A. restockingLeadTime,
	B.restockingLeadTime,
	A. sentimentScore,
	B.sentimentScore,
	A. sentimentMagnitude,
	B.sentimentMagnitude
FROM sales_report A
	join products B
	ON
	A.productSKU=B.SKU;
```
``` SQL
ALTER TABLE sales_report
	DROP COLUMN name,
	DROP COLUMN stockLevel,
	DROP COLUMN restockingLeadTime,
	DROP COLUMN sentimentScore,
	DROP COLUMN sentimentMagnitude
;
```
- ### analytics Table
According to the results of the following code, visitId and visitStartTime are almost repetitive so the second column is dropped.
``` SQL
select visitId,visitStartTime
from analytics
WHERE
visitId=visitStartTime;

ALTER TABLE analytics
	DROP COLUMN visitStartTime;
```

## 3. Deleting sales_by_sku table
By joining sales_report table and sales_by_sku table, we see that there are repetitive information on the second table, so we can entirely delete the second table. FYI, There are only 8 more rows in second table which can be ignored as these information would not be used in our analysis.
``` SQL
SELECT 
	A.productSKU,
	B.productSKU,
	A.total_ordered,
	B.total_ordered
FROM sales_report A
	join sales_by_sku B
	ON
	A.productSKU=B.productSKU;
```
``` SQL
DROP TABLE sales_by_sku;
```

## 4. Deleting null columns
- ### analytics Table
Detecting that userid columns is null, so it is dropped.
``` SQL
SELECT 
	COUNT(*)
FROM 
	analytics
WHERE
    userid IS NULL;
```
Detetcting that most values for ***"bounces"*** and ***"revenue"*** columns are null.
``` SQL
SELECT 
	COUNT(*)
FROM 
	analytics
WHERE
    bounces IS NULL
	AND
	revenue IS NULL;
```
Dropping useless columns
``` SQL
ALTER TABLE analytics
	DROP COLUMN userid,
	DROP COLUMN bounces,
	DROP COLUMN revenue; 
```

- ###  all_sessions Table
With the same method used for analytics table, Null columns were detected, so we drop a number of columns that majority of their data is Null.
```SQL
ALTER TABLE all_sessions
	DROP COLUMN productRefundAmount,
	DROP COLUMN itemQuantity,
	DROP COLUMN itemRevenue,
	DROP COLUMN transactions,
	DROP COLUMN totalTransactionRevenue,
	DROP COLUMN sessionQualityDim,
	DROP COLUMN productQuantity, 
	DROP COLUMN prodtransactionRevenue, 
	DROP COLUMN prodtransactionRevenue, 
	DROP COLUMN searchKeyword,
	DROP COLUMN prodtransactionRevenue; 
```
## 5.Making big numbers smaller
```SQL
UPDATE all_sessions
	SET productPrice = all_sessions.productPrice/10^6;
```
## 6.Rename a Column Name
In products table, SKU column name is updated for consistancy reasons.
``` SQL
ALTER TABLE products
  RENAME COLUMN sku TO productsku;
```
 ## 7. Deleting Useless columns
 all_sessions table has some useless columns that should be dropped. 99% of ***"Productvariant"***  column are not set. In ***"currency Code"*** column, either the values are USD or Null. 
 ``` SQL
 SELECT 
	COUNT(*)
FROM 
	all_sessions
WHERE
	productVariant ='(not set)';
```
``` SQL
SELECT 
	COUNT(*)
FROM 
	all_sessions
WHERE
	currencyCode ='USD'
	OR
	currencyCode is NULL;
```
``` SQL
ALTER TABLE all_sessions

	DROP COLUMN productVariant, 
	DROP COLUMN currencyCode; 
```