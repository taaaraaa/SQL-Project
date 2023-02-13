# What issues will you address by cleaning the data?
- Removing duplicates
- Deleting empty coloumns
- Making big numbers smaller by deviding them by 10^6


# Below, provide the SQL queries you used to clean your data.

## Removing duplicates
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
## Deleting empty columns
```SQL
ALTER TABLE all_sessions
	DROP COLUMN productRefundAmount,
	DROP COLUMN itemQuantity,
	DROP COLUMN itemRevenue; 
```
## Making big numbers smaller
```SQL
UPDATE all_sessions
	SET productPrice = all_sessions.productPrice/10^6;
```
