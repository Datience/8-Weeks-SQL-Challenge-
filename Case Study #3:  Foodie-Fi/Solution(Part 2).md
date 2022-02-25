Refer to Schema in Part I

# PART II. Data Analysis Questions

### 1. How many customers has Foodie-Fi ever had?

```sql
SELECT  
count(distinct customer_id) as count_customers
FROM subscriptions;
```
![image](https://user-images.githubusercontent.com/80718915/155772442-3a1f8bd8-708e-48b8-8051-7137468cc539.png)

**Answer*: Foodie-Fi has 1000 customers.

### 2. 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value

```sql
SELECT 
 DATEPART(month, start_date) month --DATEPART function here, extracts the month out of the start_date
, COUNT(customer_id) count_trialplan
FROM subscriptions s
JOIN plans p on p.plan_id = s.plan_id
WHERE P.plan_id = 0
GROUP BY DATEPART(month, start_date)
ORDER BY month -- to see the count from January to Decemeber 
--ORDER BY count_trialplan -- to see the count of trial plan from highest to lowest
```
![image](https://user-images.githubusercontent.com/80718915/155772822-0b75a46d-4d93-446c-857b-47e09f959bc2.png)

**Answer*: February had the lowest count of trail plans and March has the highest. 

### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name

```sql
SELECT 
p.plan_id
, p.plan_name
, count(p.plan_id) as count_2021
FROM subscriptions s
JOIN plans p on p.plan_id = s.plan_id
WHERE DATEPART(YEAR, start_date) != '2020'
group by p.plan_id, p.plan_name
ORDER BY p.plan_id;
```
![image](https://user-images.githubusercontent.com/80718915/155773039-e4188361-db73-4840-972a-91176b4625f6.png)

**Answer:** There were no customer on the trial plan in 2021. This either tells us that there were no new customers in 2021 or the customer's sugned up for other plans straightaway. 


### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?

```sql
SELECT 
  CAST (CAST(100 * COUNT(*) AS NUMERIC)/ (SELECT COUNT(DISTINCT customer_id) FROM subscriptions) AS DECIMAL (10,1))  churn_percent
, COUNT(customer_id)  churn_count
FROM subscriptions s
JOIN plans p on p.plan_id = s.plan_id
WHERE P.plan_id = 4;
```
![image](https://user-images.githubusercontent.com/80718915/155800801-55fed767-cd71-4c5e-aa52-94fd70e0a2ac.png)

**Answer**: 30.7% or 307 customers have churned 

### 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?

```sql
SELECT 
  COUNT (*) as churn_count
, CAST (CAST(100 * COUNT(*) as numeric)/ (SELECT COUNT(distinct customer_id) FROM subscriptions) AS DECIMAL (10,0)) churn_percent FROM (
	SELECT 
	p.plan_id 
	, customer_id
	, ROW_NUMBER() OVER (PARTITION BY s.customer_id ORDER BY s.plan_id) as ranking
FROM subscriptions s
JOIN plans p on p.plan_id = s.plan_id) t
WHERE t.plan_id = 4 
AND ranking = 2;
```
![image](https://user-images.githubusercontent.com/80718915/155801004-0994e2c0-12ae-4e35-bcf9-720167357b7b.png)

**Answer**: 92 customer or 9% of customers have churned straight after their initial free trial

### 6. What is the number and percentage of customer plans after their initial free trial?

```sql
SELECT 
planid
,  COUNT(*) as number_of_customer
,  CAST (CAST(100 * COUNT(*) as numeric)/ (SELECT COUNT(distinct customer_id) FROM subscriptions) as decimal (10,1)) percent_of_customer

FROM  (
	SELECT  
	customer_id
	, p.plan_id planid 
	,plan_name
	,ROW_NUMBER() OVER (PARTITION BY s.customer_id ORDER BY s.plan_id) as ranking
	FROM subscriptions s
JOIN plans p on p.plan_id = s.plan_id) t
WHERE  t.planid = 0 
OR ranking = 2
GROUP by planid
ORDER by planid ;
```
![image](https://user-images.githubusercontent.com/80718915/155801185-d5dfbcef-d143-41ee-992a-8ce94deaffd7.png)

**Answer** : We can see that over 85% of customers are on paid plans and only less than 5% are on pro-annual which costs $199. Therefore, food-fi should focus to bring in more customers who are willing to switch to pro annual 

### 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?

```sql
SELECT
planid
,  COUNT(*) as number_of_customer
,  CAST (CAST(100 * COUNT(*) as numeric)/ (SELECT COUNT(distinct customer_id) FROM subscriptions) as decimal (10,1)) percent_of_customer

FROM  (
	SELECT  
	customer_id
	, p.plan_id planid 
	,plan_name
	, start_date
	, LEAD(start_date, 1) OVER(PARTITION BY customer_id ORDER BY start_date) as lead_date
	FROM subscriptions s
JOIN plans p on p.plan_id = s.plan_id
WHERE start_date <= '2020-12-31') t
WHERE  t.planid = 0 
AND (lead_date IS NOT NULL AND (start_date < '2020-12-31' AND lead_date > '2020-12-31'))
     OR (lead_date IS NULL AND start_date < '2020-12-31')
GROUP BY planid
ORDER BY planid ;
``` 
![image](https://user-images.githubusercontent.com/80718915/155801357-ea373d38-7a76-42ca-aac6-876f6a128b02.png)

### 8. How many customers have upgraded to an annual plan in 2020?
```sql
SELECT  
COUNT( customer_id) as number_of_customer
FROM subscriptions s
WHERE start_date < '2021-01-01' 
AND plan_id = 3 ;
```
![image](https://user-images.githubusercontent.com/80718915/155801512-cf59b5aa-3d9b-4367-853e-b1bd6c927939.png)

**Answer**: 195 customers have upgraded to annual plan in 2020.


### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?

```sql
SELECT 
  customer_id, 
  start_date AS trial_date
into #TD
FROM subscriptions
WHERE plan_id = 0 ;

SELECT 
  customer_id, 
  start_date AS annual_date
into #AD
FROM subscriptions
WHERE plan_id = 3 ;

SELECT 
  AVG(DATEDIFF (day, #TD.trial_date, #AD.annual_date)) as average_days
FROM #TD
JOIN #AD
  ON #TD.customer_id = #AD.customer_id;
```
![image](https://user-images.githubusercontent.com/80718915/155801665-14e1cc7f-ee9c-44c8-bded-a09bd5d9b0e2.png)

**Answer**: On average, it takes 105 days for a customer to upgrade from trial plan to annual plan 

### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)

*Learning how to create buckets in SQL, will come back to this.*

### 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?

```sql
SELECT
 COUNT(*) as number_downgraded_customer
FROM  (
	SELECT  
	customer_id
	, p.plan_id planid 
	,plan_name
	, start_date
	, LEAD(p.plan_id, 1) OVER(PARTITION BY customer_id ORDER BY start_date) as lead_date
	FROM subscriptions s
JOIN plans p on p.plan_id = s.plan_id
WHERE start_date <= '2020-12-31') t
WHERE  t.planid = 2
and lead_date = 1;
```
![image](https://user-images.githubusercontent.com/80718915/155801906-15f9d9bc-f2b0-4b3d-9d79-01da5f51ec0b.png)

**Answer:** There were 0 customer's that downgraded from a pro-monthly to a basic monthly plan in 2020


