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


