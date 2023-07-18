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
(fill in what you discovered this data could tell you and how you used the data to answer those questions)

## Challenges 
- The Time column's unit is undefined. It is not UNIX time. Time in seconds and microseconds did not make sense either. 
- The default productprice in the original all_sessions table was to be divided by 1,000,000. I assumed that the totalTransactionRevenue was also to be divided be 1,000,000. The question marks would be around why the product price of one unit is not equal to the totaltransactionrevenue for that product. Sometimes the productprice would be larger than the totaltransactionrevenue. 
- The all_sessions table was missing data in its columns. For example the currencycode had Null values when they really should have been marked as USD (all rows had USD as the currencycode regardless of country).
- The fullvisitorid and the visitid in all_sessions were not Primary Keys as they had duplicates. The SKUs and ProductSKUs were also not Primary Keys as they also had duplicates. Therefore, joining tables was possible, but may not produce accurate results.
- The title of the columns had capitalization and make querying harder. Therefore, I made all column titles lowercase.
- There would some productprices that were zero (not null). None of the transactions had a productprice of zero. Therefore, I left those 0 productprices as is, as it would not impact the scope of this project. 

## Future Goals
- Explore tables other than all_sessions in the ecommerce database like sale_report, sales_by_sku, and products.
- Find out if there are any products that are returned from customers back to the company more than others.

