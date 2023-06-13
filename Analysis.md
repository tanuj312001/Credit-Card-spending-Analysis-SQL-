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



### 3. Query to print 3 columns: city, highest_expense_type , lowest_expense_type.

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



### 4. Query to find percentage contribution of spends by females for each expense type

````sql
with spend_by_female as (
SELECT Exp_type, SUM(Amount) as female_spend
FROM credit
WHERE Gender='F'
GROUP BY Exp_type
),
 total_spend as (
SELECT Exp_type, SUM(Amount) as total_spend
FROM credit
GROUP BY Exp_type
)

SELECT F.exp_type, F.female_spend, T.total_spend,  F.female_spend*100/T.total_spend as female_pct
FROM spend_by_female F JOIN total_spend T
ON F.Exp_type=T.Exp_type

````

**Results**

| Expense Type    | Female Spend   | Total Spend    | Female %       |
| --------------- | -------------- | -------------- | -------------- |
| Bills           | 580,035,469    | 907,072,473    | 63.9459%       |
| Food            | 452,817,279    | 824,724,009    | 54.9053%       |
| Entertainment   | 358,663,333    | 726,437,536    | 49.3729%       |
| Grocery         | 365,646,998    | 718,207,923    | 50.9110%       |
| Fuel            | 392,282,421    | 789,135,821    | 49.7104%       |
| Travel          | 55,865,530     | 109,255,611    | 51.1329%       |


### 5. Which card and expense type combination saw highest month over month growth in Jan-2014?

````sql
with month_year_spend as (
	SELECT  
	card_type, 
	exp_type, 
	MONTH (str_to_date(date, '%d-%b-%y')) AS spend_month, 
	YEAR (str_to_date(date, '%d-%b-%y')) AS spend_year
,
	SUM(amount) AS spend
	From credit
	GROUP BY card_type, exp_type, spend_month, spend_year
)	
,get_prev_spend AS (
SELECT *, LAG(spend) OVER(PARTITION BY card_type,exp_type ORDER BY spend_year,spend_month) 
AS lag_spend
FROM  month_year_spend
)

SELECT *,
(spend-lag_spend) AS  growth
FROM get_prev_spend
WHERE spend_month=1 and spend_year=2014 and (spend-lag_spend)>0
ORDER BY  (spend-lag_spend) DESC LIMIT 1;

````

**Results**

| card_type | exp_type | spend_month | spend_year | spend       | lag_spend   | growth      |
| --------- | -------- | ----------- | ---------- | ----------- | ----------- | ----------- |
| Platinum  | Grocery  | 1           | 2014       | 12,256,343  | 7,757,562   | 4,498,781   |



### 6. During weekends which city has highest total spend to total no of transcations ratio?

````sql
with weekend as (
SELECT *,CASE WHEN DAYOFWEEK(STR_TO_DATE(date, '%d-%b-%y')) IN (0,7) THEN 'Weekend' ELSE
'Not Weekend' END AS is_weekend
FROM credit 
),
 weekend_city_trans AS (
SELECT City, SUM(amount) AS total_spend, COUNT(*) as total_transactions
FROM weekend
WHERE is_weekend='Weekend'
GROUP BY City
)

SELECT *, total_spend/total_transactions as ratio
FROM weekend_city_trans
ORDER BY ratio DESC 
LIMIT 1
````

**Results**

| City                    | total_spend | total_transactions | ratio       |
| ----------------------- | ----------- | ------------------ | ----------- |
| Raghogarh-Vijaypur, India | 299,980     | 1                  | 299,980.0000|




### 7. which city took least number of days to reach its 500th transaction after first transaction in that 

````sql
with first_trans as (
SELECT City, rnk, STR_TO_DATE(date, '%d-%b-%y') as date
FROM (SELECT *, 
DENSE_RANK() OVER(PARTITION BY city ORDER BY STR_TO_DATE(date, '%d-%b-%y') ASC) as rnk 
FROM credit) X
WHERE X.rnk=1
),
fivehundred_trans as (
SELECT City, rnk, STR_TO_DATE(date, '%d-%b-%y') as date
FROM (SELECT *, 
ROW_NUMBER() OVER(PARTITION BY city ORDER BY STR_TO_DATE(date, '%d-%b-%y') ASC) as rnk 
FROM credit) X
WHERE X.rnk=500
)
SELECT f.city as city, f.date as first_trans_date,
l.date as five_hundred_trans
FROM first_trans f JOIN fivehundred_trans l 
ON f.city=l.city 
ORDER BY l.date-f.date LIMIT 1
````

**Results**

| city            | first_trans_date | five_hundred_trans |
| --------------- | ---------------- | ------------------ |
| Bengaluru, India| 2013-10-04       | 2013-12-24         |

