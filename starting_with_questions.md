Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

```SQL
SELECT country, SUM(totalTransactionRevenue)
	FROM all_sessions
	GROUP BY country
	Order BY SUM(totalTransactionRevenue);

SELECT city, SUM(totalTransactionRevenue)
	FROM all_sessions
	GROUP BY city
	Order BY SUM(totalTransactionRevenue);
```
Answer: United States in countries, Sanfransisco in cities




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
```SQL
SELECT city, AVG(productQuantity)
	FROM all_sessions
	GROUP BY city
	Order BY AVG(productQuantity);

SELECT country, AVG(productQuantity)
	FROM all_sessions
	GROUP BY country
	Order BY SUM(productQuantity);
```

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```SQL
Select city, v2ProductCategory 
	From all_sessions 
	Group By v2ProductCategory, city
	Order By city

Select country, v2ProductCategory 
	From all_sessions 
	Group By v2ProductCategory, country
	Order By country
```

Answer: Yes there is

**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

``` SQL
SQL Queries:
SELECT country,  MAX(productQuantity), v2ProductCategory
	FROM all_sessions
	GROUP BY country, v2ProductCategory	
	Order BY MAX(productQuantity);

SELECT city,  MAX(productQuantity), v2ProductCategory
	FROM all_sessions
	GROUP BY city, v2ProductCategory	
	Order BY MAX(productQuantity);
```


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

``` SQL
SELECT city, SUM(totalTransactionRevenue),
    100*(SUM(totalTransactionRevenue)/(select SUM(totalTransactionRevenue) from all_sessions)) 
        as sales_percentage
	FROM all_sessions
	GROUP BY city
	Order BY sales_percentage;

SELECT country, SUM(totalTransactionRevenue),
    100*(SUM(totalTransactionRevenue)/(select SUM(totalTransactionRevenue) from all_sessions)) 
        as sales_percentage
	FROM all_sessions
	GROUP BY country
	Order BY sales_percentage;
```

Answer:
Yes, we can calculate each city/country contributed to what percentage of total revenue






