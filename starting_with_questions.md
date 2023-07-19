Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

#1A) Preferred Method - By City 

SELECT DISTINCT city, round(AVG(totaltransactionrevenue), 2) as avg_transactionrev_by_city, currencycode, COUNT(totaltransactionrevenue)
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, currencycode, totaltransactionrevenue
ORDER BY avg_transactionrev_by_city DESC;

#1B) Preferred Method - By Country

SELECT DISTINCT country, ROUND(AVG(totaltransactionrevenue), 2) as avg_transactionrev_by_country, COUNT(totaltransactionrevenue)
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, currencycode
ORDER BY avg_transactionrev_by_country DESC NULLS LAST;

#Bonus Method - one query with WINDOW function 

SELECT DISTINCT city, ROUND(AVG(totaltransactionrevenue), 2) as avg_transactionrev_by_city,
country, ROUND(AVG(totaltransactionrevenue) OVER(PARTITION BY Country),2) AS avg_transactionrev_by_country,
currencycode, COUNT(totaltransactionrevenue)
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, city, currencycode, totaltransactionrevenue
ORDER BY avg_transactionrev_by_city DESC;


Answer:

#1A) CITY
#1) Atlanta 742.48 USD - Count 1
#2) Sunnyvale 649.24 USD - Count 1
#3) Tel Aviv-Yafo 602.00 USD - Count 1
#Limitation as it appears that the sample size is small - count of one for each city here

#1B) COUNTRY
#1) Israel - 602.00 USD - Count 1
#2) Australia - 358.00 USD - Count 1
#3) United States - 171.72 USD - Count 77


**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

#2A) By City

SELECT DISTINCT city, ROUND(AVG(totaltransactionrevenue/productprice),0) AS av_prodord_city, COUNT(totaltransactionrevenue) AS num_of_transactions
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city
ORDER BY av_prodord_city DESC;

#2B) By Country

SELECT DISTINCT country, ROUND(AVG(totaltransactionrevenue/productprice),0) AS av_prodord_country, COUNT(totaltransactionrevenue) AS num_of_transactions
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country
ORDER BY av_prodord_country DESC NULLS LAST;



Answer:

#2A) CITY
#1) Sunnyvale - 117 products ordered on average - over 4 transactions
#2) Atlanta - 36 products ordered on average - over 2 transactions
#3) Chicago - 17 products ordered on average - over 3 transactions
#Notes:
#Limitation as it appears that the sample size is small. The average products ordered came out as a float. Meaning the data from the dataset is not the most accurate, because we expect revenue/product price to give a whole number for quantity. 
#The discrepancy in price could be due to discounts we have not taken into consideration. The data could have also come from a third party.
#Averages could be off due to rounding down or up based on normal rounding protocol.

#2B) COUNTRY
#1) 18 products ordered on average by United States over 77 transactions
#2) 8 products ordered on average by Israel over 1 transaction
#3) 4 products ordered on average by Canada over 1 transaction
#Notes:
#Risk as it appears that the sample size is small. The average products ordered came out as a float. Meaning the data from the dataset is not the most ideal or accurate, because we expect revenue/product price to give a whole number for quantity. 
#These discrepancy in price could be due to discounts we have not taken into consideration. The data could have also come from third party.)
#Averages could be off due to rounding down or up based on normal rounding protocol.



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

#3A) City- By WEST COAST

WITH CTE AS(SELECT Distinct city, v2productname, totaltransactionrevenue/productprice AS top_prodord_city, v2productcategory,
RANK() OVER(PARTITION BY CITY ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.city, CTE.v2productname, ROUND(CTE.top_prodord_city, 0) AS top_prodord_city, v2productcategory
FROM CTE
WHERE CTE.city IN('San Francisco', 'Sunnyvale', 'Mountain View', 'Los Angeles', 'San Jose', 'San Bruno', 'Seattle')
GROUP BY CTE.city, CTE.v2productcategory, CTE.top_prodord_city, CTE.v2productname
ORDER BY city, CTE.v2productcategory;


#3B) City- By Midwest

WITH CTE AS(SELECT Distinct city, v2productname, totaltransactionrevenue/productprice AS top_prodord_city, v2productcategory,
RANK() OVER(PARTITION BY CITY ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.city, CTE.v2productname, ROUND(CTE.top_prodord_city, 0) AS top_prodord_city, v2productcategory
FROM CTE
WHERE CTE.city IN('Columbus', 'Chicago', 'Nashville')
GROUP BY CTE.city, CTE.v2productcategory, CTE.top_prodord_city, CTE.v2productname
ORDER BY city, CTE.v2productcategory;

#3C) City- By EAST COAST

WITH CTE AS(SELECT Distinct city, v2productname, totaltransactionrevenue/productprice AS top_prodord_city, v2productcategory,
RANK() OVER(PARTITION BY CITY ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.city, CTE.v2productname, ROUND(CTE.top_prodord_city, 0) AS top_prodord_city, v2productcategory
FROM CTE
WHERE CTE.city IN('Atlanta', 'New York')
GROUP BY CTE.city, CTE.v2productcategory, CTE.top_prodord_city, CTE.v2productname
ORDER BY city, CTE.v2productcategory;

#3D) City- By GULF COAST

WITH CTE AS(SELECT Distinct city, v2productname, totaltransactionrevenue/productprice AS top_prodord_city, v2productcategory,
RANK() OVER(PARTITION BY CITY ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.city, CTE.v2productname, ROUND(CTE.top_prodord_city, 0) AS top_prodord_city, v2productcategory
FROM CTE
WHERE CITY IN('Austin', 'Houston')
GROUP BY CTE.city, CTE.v2productcategory, CTE.top_prodord_city, CTE.v2productname
ORDER BY city, CTE.v2productcategory;


#BY COUNTRY

#3E) Patterns By COUNTRY - Nest
#Used "OR v2productname LIKE '%Nest%'" because 1 Nest product ordered had an incorrect category name

WITH CTE AS(SELECT Distinct country, v2productname, totaltransactionrevenue/productprice AS prodord_country, v2productcategory,
RANK() OVER(PARTITION BY country ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.country, CTE.v2productname, ROUND(CTE.prodord_country, 0) AS prodord_country, v2productcategory
FROM CTE
WHERE CTE.V2productcategory LIKE '%Nest%' OR v2productname LIKE '%Nest%'
GROUP BY CTE.country, CTE.v2productcategory, CTE.prodord_country, CTE.v2productname
ORDER BY country, CTE.v2productcategory;

#3F) Patterns BY COUNTRY - Apparel

WITH CTE AS(SELECT Distinct country, v2productname, totaltransactionrevenue/productprice prodord_country, v2productcategory,
RANK() OVER(PARTITION BY country ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.country, CTE.v2productname, ROUND(CTE.prodord_country, 0) AS prodord_country, v2productcategory
FROM CTE
WHERE CTE.V2productcategory LIKE ('%pparel%') OR v2productname LIKE '%shirt%' OR v2productname LIKE 'Women%s' 
OR v2productname LIKE 'Men%s' 
GROUP BY CTE.country, CTE.v2productcategory, CTE.prodord_country, CTE.v2productname
ORDER BY country, CTE.v2productcategory;

#3G) Patterns By COUNTRY - United States

WITH CTE AS(SELECT Distinct country, v2productname, totaltransactionrevenue/productprice AS prodord_country, v2productcategory,
RANK() OVER(PARTITION BY country ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.country, CTE.v2productname, ROUND(CTE.prodord_country, 0) AS prodord_country, v2productcategory
FROM CTE
WHERE CTE.country = 'United States'
GROUP BY CTE.country, CTE.v2productcategory, CTE.prodord_country, CTE.v2productname
ORDER BY country, CTE.v2productcategory;

#3H) Patterns By Country - Product Categories

WITH CTE AS(SELECT Distinct country, v2productname, totaltransactionrevenue/productprice prodord_country, v2productcategory,
RANK() OVER(PARTITION BY country ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.country, CTE.v2productname, ROUND(CTE.prodord_country, 0) AS prodord_country, v2productcategory
FROM CTE
GROUP BY CTE.country, CTE.v2productcategory, CTE.prodord_country, CTE.v2productname
ORDER BY country, CTE.v2productcategory;


Answer:

#3A) City- By WEST COAST: 11/30 (36.7%) of transactions had product categories that were apparel and 11/30 transactions (36.7%) were NEST products (of which, 36% of NEST products bought were camera related).

#3B) City- By Midwest: 2/5 (40%) of transactions had product categories that were Nest products. The other types included writing instruments, lifestyle (sunglasses), and apparel.

#3C) City- By EAST COAST: 6/11 (55%) of transactions had their product category as apparel.

#3D) City- By GULF COAST: 2/3 of transactions had product category as apparel.

#3E) Patterns By COUNTRY - Nest: 28/81 of transactions worldwide had their product category as Nest products

#3F) Patterns BY COUNTRY - Apparel: 26/81 (32%) of transactions worldwide had the product category as apparel.

#3G) Patterns By COUNTRY - United States: 24/77 of the transactions from the United States were labelled apparel. 28/77 of the transactions from the United States were labelled Nest products.

#3H) Patterns By Country - Product Categories: The United States dominates the products ordered and product category type ordered with 77/81 of total worldwide transactions being ordered from US visitors.
#Apparel and Nest products dominate the category of products ordered worldwide. 
#Vistors from Australia and Israel ordered NEST products. Visitors from Canada and Switzerland ordered apparel. 
#The most prominent product categories ordered from the United States were Nest products and apparel composing 52/77 (67.5%) of transactions.




**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries

#4A) City

WITH CTE AS(SELECT Distinct city, v2productname, totaltransactionrevenue/productprice top_prodord_city, v2productcategory,
RANK() OVER(PARTITION BY CITY ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.city, CTE.v2productname, ROUND(CTE.top_prodord_city, 0) AS top_prodord_city, v2productcategory, CTE.ranks
FROM CTE
GROUP BY CTE.city, CTE.top_prodord_city, cte.ranks, CTE.v2productname, CTE.v2productcategory
HAVING CTE.ranks = 1
ORDER BY city, ranks;


#City patterns by region
#Asumptions that the city names are major city names

#4B) City- By WEST COAST

WITH CTE AS(SELECT Distinct city, v2productname, totaltransactionrevenue/productprice top_prodord_city, v2productcategory,
RANK() OVER(PARTITION BY CITY ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.city, CTE.v2productname, ROUND(CTE.top_prodord_city, 0) AS top_prodord_city, v2productcategory, CTE.ranks
FROM CTE
WHERE CTE.city IN('San Francisco', 'Sunnyvale', 'Mountain View', 'Los Angeles', 'San Jose', 'San Bruno', 'Seattle')
GROUP BY CTE.city, CTE.top_prodord_city, cte.ranks, CTE.v2productname, CTE.v2productcategory
HAVING CTE.ranks = 1
ORDER BY city, ranks;

#4C) City- By Mid-WEST

WITH CTE AS(SELECT Distinct city, v2productname, totaltransactionrevenue/productprice top_prodord_city, v2productcategory,
RANK() OVER(PARTITION BY CITY ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.city, CTE.v2productname, ROUND(CTE.top_prodord_city, 0) AS top_prodord_city, v2productcategory, CTE.ranks
FROM CTE
WHERE CTE.city IN('Columbus', 'Chicago', 'Nashville')
GROUP BY CTE.city, CTE.top_prodord_city, cte.ranks, CTE.v2productname, CTE.v2productcategory
HAVING CTE.ranks = 1
ORDER BY city, ranks;

#4D) City- By EAST COAST

WITH CTE AS(SELECT Distinct city, v2productname, totaltransactionrevenue/productprice top_prodord_city, v2productcategory,
RANK() OVER(PARTITION BY CITY ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.city, CTE.v2productname, ROUND(CTE.top_prodord_city, 0) AS top_prodord_city, v2productcategory, CTE.ranks
FROM CTE
WHERE CTE.city IN('Atlanta', 'New York')
GROUP BY CTE.city, CTE.top_prodord_city, cte.ranks, CTE.v2productname, CTE.v2productcategory
HAVING CTE.ranks = 1
ORDER BY city, ranks;

#4E) City- By GULF COAST

WITH CTE AS(SELECT Distinct city, v2productname, totaltransactionrevenue/productprice top_prodord_city, v2productcategory,
RANK() OVER(PARTITION BY CITY ORDER BY totaltransactionrevenue/productprice DESC) AS ranks
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.city, CTE.v2productname, ROUND(CTE.top_prodord_city, 0) AS top_prodord_city, v2productcategory, CTE.ranks
FROM CTE
WHERE CITY IN ('Austin', 'Houston')
GROUP BY CTE.city, CTE.top_prodord_city, cte.ranks, CTE.v2productname, CTE.v2productcategory
HAVING CTE.ranks = 1
ORDER BY city, ranks;


#4F) Country

WITH CTE AS(SELECT Distinct country, v2productname, totaltransactionrevenue/productprice top_prodord_country, 
RANK() OVER(PARTITION BY country ORDER BY totaltransactionrevenue/productprice DESC) AS ranks, v2productcategory
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, totaltransactionrevenue/productprice, v2productname, v2productcategory)

SELECT CTE.country, CTE.v2productname, ROUND(CTE.top_prodord_country, 0) AS top_prodord_country, CTE.v2productcategory, CTE.ranks
FROM CTE
GROUP BY CTE.country, CTE.top_prodord_country, cte.ranks, CTE.v2productname, CTE.v2productcategory
HAVING CTE.ranks = 1
ORDER BY country, ranks;


Answer:

#4A) There are too many cities to list. Below are a few examples:

Sunnyvale - SPF-15 Slim & Slender Lip Balm - 464 products
Atlanta - Reusable Shopping Bag - 66 products
Chicago - Google Sunglasses - 44 products

#4B) City- By WEST COAST: 4/7 cities with recorded transactions had the apparel category as their most popular item 
Sunnyvale: - SPF-15 Slim & Slender Lip Balm most popular item

#4C) City- By Midwest: Visitors from cities in the Midwest had Google Sunglasses (lifestyle category) as their most ordered product.

#4D) City- By EAST COAST: Reusable shopping bags were the most popular products ordered on the East Coast followed by Google Laptop/Cellphone stickers.

#4E) City- By GULF COAST: Google Men's 100% Cotton Short Sleeve Hero Tee Navy in the "Shop by Brand" category was the most popular product in the Gulf Coast. 

#4F) By Country: TOP SELLING PRODUCTS

Australia - Nest® Cam Indoor Security Camera - USA
Canada - Google Men's 3/4 Sleeve Raglan Henley Grey
Israel - Nest® Protect Smoke + CO Black Wired Alarm-USA
Switzerland - YouTube Men's 3/4 Sleeve Henley
United States - SPF-15 Slim & Slender Lip Balm

#Notes:
#Quantity of product is a decimal. Indicating that (with more time) the revenue should be further examined, 
#Assuming v2productname in all_sessions is the most up-to-date name and accurately reflects the current name of the product

**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

#5A) Preferred Method: By City
SELECT DISTINCT city, SUM(totaltransactionrevenue) as tot_transactionrevenue_by_city, currencycode
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY city, currencycode, totaltransactionrevenue
ORDER BY tot_transactionrevenue_by_city DESC;

#5B) Preferred Method: By Country
SELECT DISTINCT country, SUM(totaltransactionrevenue) as tot_transaction_by_country, currencycode
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, currencycode
ORDER BY tot_transaction_by_country DESC;

#Bonus Method - Utilizing WINDOW function in one query to return both city and country in one table.

SELECT DISTINCT city, SUM(totaltransactionrevenue) as tot_transaction_by_city,
country, SUM(totaltransactionrevenue) OVER(PARTITION BY Country),
currencycode
FROM all_sessionso
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, city, currencycode, totaltransactionrevenue
ORDER BY tot_transaction_by_city DESC;



Answer:


#5A) By CITY
#1) Atlanta 742.48 USD
#2) Sunnyvale 649.24 USD
#3) Tel Aviv-Yafo 602.00 USD

#5B) By COUNTRY
#1) United States - 13222.16 USD
#2) Israel - 602.00 USD
#3) Australia - 358.00 US

