# SQL_Associate_Data_Analyst

FoodYum is a Grocery Store chain based in United Sates, it offers a wide range of products like meat, dairy, baked goods, and other household food staples.
This README file  provides additional information and SQL queries to perform requested data analysis tasks on the product data.


# Identifying Missing year_added Values <a name="a"></a>
The first task is to determine how many products have missing values on the year_added field. Here is the Query used to complete this task:

```SQL
SELECT COUNT(*) AS missing_year
FROM Products
WHERE year_added IS  NULL;
```
This query outpus is a single column indicating the number of products with missing year_added values.

# Ensuring Data Accuracy <a name="b"></a>
This task requires the data to be cleaned and to match each column name to the given criteria. The Query used to perform this is the following:

```SQL
SELECT 
product_id,
COALESCE(product_type,'Unknown') AS product_type,
COALESCE((NULLIF(REPLACE(brand,'-',''),'')),'Unknown') AS brand,
COALESCE(ROUND(CAST(REGEXP_REPLACE(weight, '[^\d.]', '', 'g') AS DECIMAL(10, 2)), 2), ROUND((SELECT PERCENTILE_disc(0.5) WITHIN GROUP (ORDER BY CAST(REGEXP_REPLACE(weight, '[^\d.]', '', 'g') AS DECIMAL(10, 2))) FROM products), 2)) AS weight,
COALESCE(ROUND(CAST( price AS decimal(10,2)),2), CAST((SELECT PERCENTILE_cont(0.5) WITHIN GROUP (ORDER BY price) FROM products) AS decimal(10,2))) AS price,
COALESCE(average_units_sold,0) AS average_units_sold,
COALESCE(year_added,2022) AS year_added,
COALESCE(UPPER(stock_location),'Unknown') AS stock_location
FROM products
```
1. Missing product_type values were replaced with 'Unknown'
2. Missing brand values were replaced with 'Unknown', however there were no missing values but '-' instead of regular values, so it was necessary to replace them with NULL to make the COALESCE function work propperly.
3. The word 'grams' was on some of the values, so it was removed using Regexp_replace. Missing values were replaced with the overall median weight.
4. Price values were rounded to two decimal places. Missing values replaced with the overall median price.
5. Missing average_units_sold replaced with 0.
6. Missing year_added values replaced with '2022'.
7. stock_location values formatted to all uppercase Values. Missing values replaced with 'Unknown'

# Finding MIN and MAX prices for each product type

Simply using MIN and MAX functions to determine min_price and max_price for each product.

```SQL
SELECT product_type,
MIN(price) AS min_price,
MAX(price) as max_price
FROM products
GROUP BY product_type
```
# Detalied Analysis of Meat and Dairy
Finding product ID and price of meat and dairy products with average units sold greater than ten.

```SQL
SELECT product_id,
price,
average_units_sold
FROM products
WHERE product_type IN ('Meat','Dairy')
	    AND average_units_sold > 10
```
This query returns product ID, price, and avg units sold for meat and dairy products with avg units sold greater than 10

