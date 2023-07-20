Question 1: How many page views on average do visitors look at between visitors that buy something and visitors that don't?

SQL Queries:
```
SELECT transactions, ROUND(AVG(pageviews), 0)
FROM all_sessionso
GROUP BY Transactions;
```
Answer: Visitors that are buyers view more pages on average (14 pages) than non-buyers (4 pages).


Question 2: How many distinct visitors have visited from each country?

SQL Queries:
```
WITH CTE AS (SELECT country, fullvisitorid, ROW_NUMBER () OVER(PARTITION BY fullvisitorid) as dupecheck
FROM all_sessionso)
SELECT CTE.country, COUNT(CTE.fullvisitorid) AS number_of_visitors_by_country
FROM CTE
WHERE dupecheck = 1
GROUP BY country, dupecheck
ORDER BY number_of_visitors_by_country DESC;
```
Answer: There are too many cities to list. I provide a few examples below:

8118 unique visitors from the United States.
693 unique visitors from India.
606 unique visitors from Canada.
1 visitor from Luxemburg
1 visitor from Botswana


Question 3: Which quarter in 2017 had better revenue generated, Q1 or Q2?

SQL Queries:

#For Q1:
```
SELECT SUM(totaltransactionrevenue), currencycode
FROM all_sessionso
WHERE date BETWEEN '2017-1-1' AND '2017-3-31'
GROUP BY currencycode;
```
#For Q2:
```
SELECT SUM(totaltransactionrevenue), currencycode
FROM all_sessionso
WHERE date BETWEEN '2017-4-1' AND '2017-6-30'
GROUP BY currencycode;
```
Answer: Q1 had more revenue generated than Q2. Q1 generated 5498.88 USD, while Q2 generated 3128.55 USD.


Question 4: What percentage of distinct visitors to the website actually make a purchase?

SQL Queries:
```
SELECT CAST(num_transactions AS FLOAT) / num_distinct_visitors * 100 AS distinct_visitors_that_bought_as_percent
FROM
(SELECT COUNT(all_sessionso.transactions) AS num_transactions, COUNT(DISTINCT all_sessionso.fullvisitorid) AS num_distinct_visitors
FROM all_sessionso) AS subquery;
```
Answer: 0.57% of distinct visitors to the website made a purchase.

### Updated Question 4

#As of 7/19/2023, after the QA process, I discovered that there was one visitor who made two transactions. Below is my updated answer and query for Question 4.

SQL Queries:
#Method using 1) Nested Subquery to find correctedtransactioncount 2) Subquery to get num_distinct_visitors
```
SELECT CAST(
(SELECT COUNT(*) FROM (SELECT COUNT(transactions) FROM all_sessionso WHERE transactions IS NOT NULL GROUP BY fullvisitorid
HAVING COUNT(fullvisitorid) >0) correctedtransactioncount) 
AS FLOAT) / num_distinct_visitors * 100 AS distinct_visitors_that_bought_as_percent
FROM
(SELECT COUNT(DISTINCT all_sessionso.fullvisitorid) AS num_distinct_visitors
FROM all_sessionso) AS subquery1;
```
Updated Answer: 0.56% of distinct visitors to the website made a purchase.

