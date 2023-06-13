# CREDIT CARD SPENDING HABITS IN INDIA

**Creator**: Tanuj Shankarwar
**Email**: tshankarwar@ucsd.edu
**Linkedin**: https://www.linkedin.com/in/tanuj-s-662785166/

### First things first, lemme show you what data do we have 

````sql
SELECT *
FROM credit
LIMIT 5
````

**Results**
| Index | City                 | Date       | Card Type | Exp Type | Gender | Value  |
|-------|----------------------|------------|-----------|----------|--------|--------|
| 0     | Delhi, India         | 29-Oct-14  | Gold      | Bills    | F      | 82475  |
| 1     | Greater Mumbai, India| 22-Aug-14  | Platinum  | Bills   
| F      | 32555  |
| 2     | Bengaluru, India     | 27-Aug-14  | Silver    | Bills    | F      | 101738 |
| 3     | Greater Mumbai, India| 12-Apr-14  | Signature | Bills    | F      | 123424 |
| 4     | Bengaluru, India     | 05-May-15  | Gold      | Bills    | F      | 171574 |


### 1. Summary statistics of categorical variables 


| City      | Card Type | Exp Type | Gender | Month |
|-----------|-----------|----------|--------|-------|
| count     | 26052     | 26052    | 26052  | 26052 |
| unique    | 986       | 4        | 6      | 12    |
| top       | Bengaluru | Silver   | Food   | Jan   |
| freq      | 3552      | 6840     | 5463   | 13680 |


We get to know:
- Bengaluru is most frequent in transactions.
- Silver is the most used card Category type.
- Most of the transactions are done in food Category.
- Female Does the most no of transactions.


### 2. City analyis. Top 5 cities with highest spends and their percentage contribution of total credit card spends.

````sql

SELECT City, SUM(Amount) AS city_spend, ROUND(SUM(Amount) * 100 / (SELECT SUM(Amount) FROM credit),2) AS percentage
FROM credit
GROUP BY City
ORDER BY city_spend DESC
LIMIT 5;
````


**Results**

| City                  | city_spend  | percentage |
|-----------------------|-------------|------------|
| Greater Mumbai, India | 576,751,476 | 14.15      |
| Bengaluru, India      | 572,326,739 | 14.05      |
| Ahmedabad, India      | 567,794,310 | 13.93      |
| Delhi, India          | 556,929,212 | 13.67      |
| Kolkata, India        | 115,466,943 | 2.83       |

![ci](https://github.com/tanuj312001/ChicagoCrime-SQL-analysis/assets/60888384/2a474a67-19e0-48d0-84f9-681add465b39)

As we can see:
- Greater Mumbai represents most transaction amount with a percentage of 14.15
- Bengaluru actually had most transactions but comes second in terms of total transaction value.



### 3. query to print 3 columns: city, highest_expense_type , lowest_expense_type.

````sql
with total_spend_city_exp as (
SELECT City,Exp_type, SUM(Amount) as total_amount
FROM credit
GROUP BY City,Exp_type
),
highest_expense as (
SELECT A.city, A.Exp_type as highest_exp_type FROM total_spend_city_exp AS A 
INNER JOIN
(SELECT city , MAX(total_amount) as max_expense_type
FROM total_spend_city_exp
GROUP BY city) as B 
ON A.city=B.city AND A.total_amount=B.max_expense_type
),
lowest_expense as (
SELECT A.city, A.Exp_type as lowest_exp_type FROM total_spend_city_exp AS A 
INNER JOIN
(SELECT city , MIN(total_amount) as min_expense_type
FROM total_spend_city_exp
GROUP BY city) as B 
ON A.city=B.city AND A.total_amount=B.min_expense_type
)

SELECT H.city, H.highest_exp_type, L.lowest_exp_type
FROM highest_expense H INNER JOIN lowest_expense L
ON H.city=L.city 
LIMIT 15
````
**Results**

| City                  | Highest Expense Type | Lowest Expense Type |
| --------------------- | -------------------- | ------------------- |
| Ahmedabad, India      | Bills                | Grocery             |
| Delhi, India          | Bills                | Entertainment       |
| Greater Mumbai, India | Bills                | Entertainment       |
| Bengaluru, India      | Bills                | Grocery             |
| Thana Bhawan, India   | Grocery              | Entertainment       |
| Karjat, India         | Entertainment        | Fuel                |
| Sillod, India         | Fuel                 | Grocery             |
| Udaipurwati, India    | Grocery              | Entertainment       |
| Vrindavan, India      | Bills                | Grocery             |
| Mudhol, India         | Entertainment        | Bills               |
| Sohna, India          | Food                 | Entertainment       |
| Chalakudy, India      | Entertainment        | Entertainment       |
| Rahuri, India         | Bills                | Entertainment       |
| Rajpipla, India       | Food                 | Bills               |
| Aizawl, India         | Food                 | Grocery             |

