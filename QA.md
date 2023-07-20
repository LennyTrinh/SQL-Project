## Below are the listed risk areas and their descriptions.

1) Some totaltransactionrevenue are smaller than the productprice. See all_sessionso table for visitid = 1476976393. This discrepancy could be due to discounts or the data could have been inaccurate due to being supplied by a third party.
2) What if a city was labelled under an incorrect country? Ideally, this would be caught in cleaning. However, after quering the first time, I found that for fullvisitorid: 8379929056656388083, the country listed for New York was Canada. This issue has been resolved now using UPDATE clause.
3) The quantity calculated using the all_sessionso information in start_with_questions.md results in a float if not rounded. We'd expect a whole number for quantity calculated from totaltransactionrevenue/productprice. I tried using JOIN table between all_sessionso table and either sales_by_sku or sale_report tables. The issue is that there are products ordered whose all_sessionso.productsku does not show in either sales_by_sku, sale_report, nor products table. All tables have a different amount of unique productskus or skus. Although sales_by_sku, sale_report, and products have primary keys, there is no foreign key that guarantees a 1:1 match for productsku in the all_sessionso table.
4) The default productprice in the original all_sessionso table was to be divided by 1,000,000. I assumed that the totalTransactionRevenue was also to be divided by 1,000,000. 
5) One peculiar result was that the result for starting_with_questions.md's questions 3 and 4 differed for the United States when calculated using a single query rather than using 2 separate queries to get the result filtered by cities or countries.
7) Of the 81 transactions, 77 were from the United States. All the other countries (Canada, Australia, Israel, and Switzerland) only had 1 transaction. Therefore, the products ordered for those countries outside of the United States were biased by those single transactions. If the company is going to use these results, I would advise them to collect more samples.
8) In the default all_sessions table, there were v2productcategories that were not useful (ex. "(Not Set)" or gibberish). Those categories were filled in with the most reasonable product categories based on their product name or other rows' v2productcategory that had the same v2productname.


## QA Process:

Below is my QA process and the SQL queries used to execute it.

#Quality control 20% of the answers for accuracy. For this project, I will verify 2/9 (22%) of the answers.

### In starting_with_questions.md - Question 3, there were 81 transactions. 77 of those transactions were from the United States and each of the other 4 countries listed only had one transaction. Let's verify if that's actually true. 

SELECT *
FROM all_sessionso
WHERE transactions IS NOT NULL AND country IN('Canada', 'Israel', 'Australia', 'Switzerland')

#Result: It's true, Canada, Israel, Australia, and Switzerland only had one transaction.




### In starting_with_data.md Question 4, the answer was 0.57% of visitors actually bought. That number seems awfully low. Let's verify it.

#Step 1: Calculate the number of distinct visitors.

Select count(distinct fullvisitorid)
FROM all_sessionso

#Result: There are 14223 distinct visitors.

#Step 2: Calculate the number of transactions

SELECT COUNT(transactions)
FROM all_sessionso

#Results: 81 transaction

#81/14223 * 100% = 0.5695%. Therefore 0.57% is an accurate calculation.
#One should also account that 1 visitor can make multiple transactions.

#Step 3: Check if there is multiple fullvisitorid that made an order (transaction = 1)

SELECT fullvisitorid
FROM all_sessionso
WHERE transactions IS NOT NULL
GROUP BY fullvisitorid
HAVING COUNT(fullvisitorid) > 1

#Result: The query returns fullvisitorid 3764227345226401562

#Step 4: Find out if the products purchased was a duplicate or whether the visitor ordered different products.

SELECT *
FROM all_sessionso
WHERE transactions IS NOT NULL AND fullvisitorid = 3764227345226401562

#Result: Visitor (fullvisitorid = 3764227345226401562) ordered two products. They ordered "Nest® Cam Indoor Security Camera - USA" AND "Nest® Learning Thermostat 3rd Gen-USA" on the same visit on 2017-03-20.

STEP: 5 - calculate the true transactions from distinct visitors.

SELECT COUNT(*)
FROM
(SELECT COUNT(transactions)
FROM all_sessionso
WHERE transactions IS NOT NULL
GROUP BY fullvisitorid
HAVING COUNT(fullvisitorid) >0) subquery

#Result: There are 80 transactions from distinct visitors.

STEP 6: Get the correct "percentage of distinct visitors to the website actually make a purchase."

#80/14223 * 100% = 56%

#Result: Therefore, 0.56% of distinct visitors made a transaction.

