# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
In this project, I am to implement:

- Extracting data from a SQL database
- Cleaning, transforming and analyzing data
- Loading data into a database
- Developing and implementing a QA process to validate transformed data against raw data

## Process
### 1) Load CSV into a PostgreSQL database
### 2) Clean data 
### 3) Transform, extract, and analyze data from ecommerce (SQL database)
### 4) QA process and identify risks


## Results
###Project Questions:
Question 1: Which cities and countries have the highest level of transaction revenues on the site?
Atlanta (742.48 USD) and Sunnyvale (649.24 USD) have the highest level of average transaction revenue on the site by city. 
Israel (602 USD) and Australia (358.00 USD) have the highest level of average transaction revenue on the site by country.

Question 2: What is the average number of products ordered from visitors in each city and country?
Example: Sunnyvale visitors who bought ordered on average 117 products per transaction. There were 4 transactions from Sunnyvale.
Example: Visitors from the United States who bought, ordered on average 18 products per transaction. There were 77 transactions from the US.

Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?
I'll group the cities here to provide a better pattern for each region. You can see more details at the city level using the SQL query found in Question 3 of starting_with_questions.md.
West Coast ordered 36.7% of their transactions from apparel and 36.7% of their products from Nest categories.
Midwest cities ordered 40% of their transactions from Nest categories.
East Coast cities ordered 55% of their transaction from apparel categories.
Gulf Coast cities ordered 66.7% of their transactions from apparel categories.
Rocky Mountain: N/A
Nest products and apparel were the most popular product categories worldwide. 
The transactions from buyers in the US accounted for 95% of the purchases worldwide. 

Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?
I'll pick the top selling product from each region to better illustrate patterns.
West Coast: Sunnyvale: Best selling product: SPF-15 Slim & Slender Lip Balm
Rocky Mountain: N/A
Midwest: Chicago: Google Sunglasses
East Coast: Atlanta: Reusable Shopping bags
Gulf Coast: Google Men’s 100% Cotton Short Sleeve Hero Tee Navy

Top Selling product by Country:
United States: "SPF-15 Slim & Slender Lip Balm" (464 products ordered)
Israel: "Nest Protect Smoke + CO Black Wired Alarm - USA" (8 products ordered)
Canada - "Google Men's 3/4 Sleeve Raglan Henley Grey" (4 products ordered)
Australia - "Nest Cam Indoor Security Camera - USA" (3 products ordered)
Switzerland - "Youtube Men's 3/4 Sleeve Henley" (1 product ordered)

Question 5: Can we summarize the impact of revenue generated from each city/country?
Top 3 Total Transaction Revenue by City. Please see SQL query in starting_with_questions for more cities.
1) Atlanta: 742.48 USD
2) Sunnyvale: 649.24 USD
3) Tel Aviv-Yafo 602.00 USD

Top 3 Total Transaction Revenue by Country. Please see SQL query in starting_with_questions for more countries. 
United States: 13,222.16 USD
Israel: 602.00 USD
Australia: 358.00 USD

###Self-Made Questions
1. How many page views on average do visitors look at between visitors that buy something and visitors that don't?
2. How many distinct visitors have visited from each country?
3. Which quarter in 2017 had better revenue generated, Q1 or Q2?
4. What percentage of distinct visitors to the website actually make a purchase?

## Notice: 
I renamed all_sessions table from the ecommerce dataset as all_sessionso.

## Challenges 
- I tried to join all_sessionso table with either the sales_by_sku or sale_report table to get the top-selling product or the average number of products ordered by visitors. The issue is that the all_sessions table has productskus that are not in the sales_by_sku or sale_report table. For instance, productsku GGOEGDHQ014899 from all_sessionso does not exist in the other tables. Therefore, the results of the answers differ when I use the orderquantity from the products table or total_ordered from the sale_report table. The results from those tables also differ from the results I get when I use the amount ordered as (all.sessionso.totaltransactionrevenue/all_sessionso.productprice). Therefore, for this project, I only utilized the all_sessionso table.
- There were no primary keys in the all_sessionso table because the fullvisitorid and the visitid were not unique. The sku column is the primary key of the product table. The productsku is the primary key of the sales_by_sku and sale_report table. However, the amount of productskus or skus differs between all the tables. There is not an established foreign key that can establish a 1:1 relationship between the rows. Therefore, although joining tables was technically possible, the results may not be accurate.
- The time column's unit was undefined. It is not UNIX time. The numbers in the time column did not make sense to be viewed as seconds or milliseconds either.  
- The default productprice in the original all_sessionso table was to be divided by 1,000,000. I assumed that the totalTransactionRevenue was also to be divided by 1,000,000. There are question marks around why the productprice for one unit of a product is not equal to the totaltransactionrevenue for that product. Sometimes the productprice would be larger than the totaltransactionrevenue. 
- The all_sessionso table was missing data in its columns. For example, the currencycode had null values when they really should have been marked as USD (all other rows had USD as the currencycode regardless of country).
- The title of the columns had capitalization and made querying harder. Therefore, I made all column titles lowercase.
- There were some productprices that were zero (not null). None of the transactions had a productprice of zero. Therefore, I left those 0 productprices as is, as it would not impact the scope of this project. 

## Future Goals
- Explore tables other than all_sessions in the ecommerce database like sale_report, sales_by_sku, and products.
- Find out if there are any products that are returned from customers back to the company more than others.
