# What issues will you address by cleaning the data?
0) Convert the column titles to lowercase for easy querying. Done through pgAdmin UI.
1) Inconsistent data: For example "Colored Pencil Set" should not be costing 2,390,000 USD. Divide productprice by 1,000,000. 
2) Standardize data: Divide totaltransactionrevenue by 1,000,000 to be consistent with produceprice.
3) Correct the date format.
4) Get rid of the NULLs in currencycode column
5) Verify the city matches the country it is listed under. fullvisitorid: 8379929056656388083 incorrectly says that New York is in Canada.
6) We'll be working a lot with rows in all_sessionso where a transaction took place. Check if there are any duplicate transactions.
7) Locate and replace missing product categories from rows where a transaction occurred

# Queries:

## Provided below are the SQL queries used to clean the data from all_sessionso table in the ecommerce dataset.

#1) Divide productprice by 1,000,000

UPDATE all_sessionso
SET productprice = ROUND(productprice/1000000, 2);

#2 Divide totaltransactionrevenue by 1,000,000

UPDATE all_sessionso totaltransactionrevenue by 1,000,000
SET totaltransactionrevenue = ROUND(totaltransactionrevenue/1000000, 2);


#3 Correct date format
#STEP 1 - Create new date column. Cannot just convert numeric to date

ALTER TABLE all_sessionso
ADD COLUMN date2 DATE

#Step 2 - Convert "date" column to TEXT

ALTER TABLE all_sessionso
ALTER COLUMN "date" TYPE TEXT

#Step 3 - Change TEXT to DATE format

UPDATE all_sessionso
SET date2 = "date"::DATE

#Step 4 - Remove redundancy

ALTER TABLE all_sessionso
DROP COLUMN "date"

#STEP 5 - Clean up the table. Change the name of "date2" back to "date"


#4 Replace currencycode NULL with USD

UPDATE all_sessionso
SET currencycode = 'USD'
WHERE currencycode IS NULL;

#5 Update city = New York to correct country.

UPDATE all_sessionso
SET country = 'United States'
WHERE city = 'New York';

#Assumption that all city = 'New York' is from United States and not from Guyana or Liberia.

#6a Check if there are any duplicate transactions

Select visitid
FROM all_sessionso
WHERE transactions IS NOT NULL
GROUP BY visitid
HAVING count(visitid) > 1;

#6b visitid 1490046065 shows up twice in rows with transactions. Verify if that those transactions are duplicates.

SELECT *
FROM all_sessionso
WHERE visitid = 1490046065;

#Result: The two transaction with visitid 1490046065 involve different products. All is well.

#7a Find which v2productcategory cell is unuseful in rows where transactions occured

SELECT *
FROm all_sessionso
WHERE transactions IS NOT NULL;

#Result: There are three products that were ordered with unuseful v2productcategories
#Legend: "Product Name" - default v2productname output
#"Google Men's Short Sleeve Badge Tee Charcoal" - (not set)
#"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel" - ${escCatTitle}
#Google Women's 3/4 Sleeve Baseball Raglan Heather/Black - ${escCatTitle}

#7b Find out if there are other rows with the same product names with useable v2productcategories to replace the unuseable v2productcatgory

SELECT *
FROM all_sessionso
WHERE v2productname IN('Google Men%s Short Sleeve Badge Tee Charcoal', 'Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel','Google Women%s 3/4 Sleeve Baseball Raglan Heather/Black');

#Result:
- Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel - Has "Nest-USA" and "Home/Nest/Nest-USA/" as v2productcatgory
- Google Men%s Short Sleeve Badge Tee Charcoal (productsku = GGOEGAAJ032617) - Did not return any other rows with the same product name. However, there are similar products with a v2productcategory that we can adapt for the purpose of this project. (Google Men's 100% Cotton Short Sleeve Hero Tee White - "Home/Apparel/Men's/Men's-T-Shirts/")
- Google Women%s 3/4 Sleeve Baseball Raglan Heather/Black (productsku = GGOEGAAX0304)- Has other rows with the same productsku and v2productname (Google Women's 3/4 Sleeve Baseball Raglan Heather/Black - "Home/Apparel/Women's/Women's-T-Shirts/" - (Ex. From visitid = 1487772791)

#7c Make the product category for "Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel" consistent. Also make the product category for "Google Women%s 3/4 Sleeve Baseball Raglan Heather/Black" consistent.

UPDATE all_sessionso
SET v2productcategory = 'Home/Nest/Nest-USA/'
WHERE v2productname = 'Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel';

UPDATE all_sessionso
SET v2productcategory = 'Home/Apparel/Women''s/Women''s-T-Shirts/'
WHERE productsku = 'GGOEGAAX0304';

#7d Spot treat. Replace the unuseful v2productcategory with a more appropriate product category.

UPDATE all_sessionso
SET v2productcategory = 'Home/Apparel/Men''s/Men''s-T-Shirts/'
WHERE productsku = 'GGOEGAAJ032617';

#7e Verify that v2productcategory is updated.

SELECT *
FROM all_sessionso
WHERE transactions IS NOT NULL;

#Results: All good to go!
