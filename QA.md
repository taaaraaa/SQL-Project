What are your risk areas? Identify and describe them.
- Making sure that currency is considered when calculating revenue-related items
- Making sure that in future the columns that we are deleting won't be needed and won't be added some data 


QA Process:
Describe your QA process and include the SQL queries used to execute it.

- Verify the empty columns:

```SQL
SELECT 
	MAX(product_refund_amount), 
	MIN(product_refund_amount)
FROM all_sessions
;
```

