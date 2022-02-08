Schema

```sql
DROP TABLE IF EXISTS runners;
CREATE TABLE runners (
  "runner_id" INTEGER,
  "registration_date" DATE
);
INSERT INTO runners
  ("runner_id", "registration_date")
VALUES
  (1, '2021-01-01'),
  (2, '2021-01-03'),
  (3, '2021-01-08'),
  (4, '2021-01-15');


DROP TABLE IF EXISTS customer_orders;
CREATE TABLE customer_orders (
  "order_id" INTEGER,
  "customer_id" INTEGER,
  "pizza_id" INTEGER,
  "exclusions" VARCHAR(4),
  "extras" VARCHAR(4),
  "order_time" datetime 
);

INSERT INTO customer_orders
  ("order_id", "customer_id", "pizza_id", "exclusions", "extras", "order_time")
VALUES
  ('1', '101', '1', '', '', '2020-01-01 18:05:02'),
  ('2', '101', '1', '', '', '2020-01-01 19:00:52'),
  ('3', '102', '1', '', '', '2020-01-02 23:51:23'),
  ('3', '102', '2', '', NULL, '2020-01-02 23:51:23'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '2', '4', '', '2020-01-04 13:23:46'),
  ('5', '104', '1', 'null', '1', '2020-01-08 21:00:29'),
  ('6', '101', '2', 'null', 'null', '2020-01-08 21:03:13'),
  ('7', '105', '2', 'null', '1', '2020-01-08 21:20:29'),
  ('8', '102', '1', 'null', 'null', '2020-01-09 23:54:33'),
  ('9', '103', '1', '4', '1, 5', '2020-01-10 11:22:59'),
  ('10', '104', '1', 'null', 'null', '2020-01-11 18:34:49'),
  ('10', '104', '1', '2, 6', '1, 4', '2020-01-11 18:34:49');


DROP TABLE IF EXISTS runner_orders;
CREATE TABLE runner_orders (
  "order_id" INTEGER,
  "runner_id" INTEGER,
  "pickup_time" VARCHAR(19),
  "distance" VARCHAR(7),
  "duration" VARCHAR(10),
  "cancellation" VARCHAR(23)
);

INSERT INTO runner_orders
  ("order_id", "runner_id", "pickup_time", "distance", "duration", "cancellation")
VALUES
  ('1', '1', '2020-01-01 18:15:34', '20km', '32 minutes', ''),
  ('2', '1', '2020-01-01 19:10:54', '20km', '27 minutes', ''),
  ('3', '1', '2020-01-03 00:12:37', '13.4km', '20 mins', NULL),
  ('4', '2', '2020-01-04 13:53:03', '23.4', '40', NULL),
  ('5', '3', '2020-01-08 21:10:57', '10', '15', NULL),
  ('6', '3', 'null', 'null', 'null', 'Restaurant Cancellation'),
  ('7', '2', '2020-01-08 21:30:45', '25km', '25mins', 'null'),
  ('8', '2', '2020-01-10 00:15:02', '23.4 km', '15 minute', 'null'),
  ('9', '2', 'null', 'null', 'null', 'Customer Cancellation'),
  ('10', '1', '2020-01-11 18:50:20', '10km', '10minutes', 'null');


DROP TABLE IF EXISTS pizza_names;
CREATE TABLE pizza_names (
  "pizza_id" INTEGER,
  "pizza_name" TEXT
);
INSERT INTO pizza_names
  ("pizza_id", "pizza_name")
VALUES
  (1, 'Meatlovers'),
  (2, 'Vegetarian');

DROP TABLE IF EXISTS pizza_recipes;
CREATE TABLE pizza_recipes (
  "pizza_id" INTEGER,
  "toppings" TEXT
);
INSERT INTO pizza_recipes
  ("pizza_id", "toppings")
VALUES
  (1, '1, 2, 3, 4, 5, 6, 8, 10'),
  (2, '4, 6, 7, 9, 11, 12');


DROP TABLE IF EXISTS pizza_toppings;
CREATE TABLE pizza_toppings (
  "topping_id" INTEGER,
  "topping_name" TEXT
);
INSERT INTO pizza_toppings
  ("topping_id", "topping_name")
VALUES
  (1, 'Bacon'),
  (2, 'BBQ Sauce'),
  (3, 'Beef'),
  (4, 'Cheese'),
  (5, 'Chicken'),
  (6, 'Mushrooms'),
  (7, 'Onions'),
  (8, 'Pepperoni'),
  (9, 'Peppers'),
  (10, 'Salami'),
  (11, 'Tomatoes'),
  (12, 'Tomato Sauce');

```
### Data Cleaning 

```sql 
 -- Customer orders table consists of Nan and NULL value. Therefore, we remove that from the table 

 DROP TABLE IF EXISTS #customer_orders
 SELECT order_id, customer_id, pizza_id, order_time
, CASE WHEN exclusions = 'null' or exclusions like '%NaN%' 
	THEN '' 
	ELSE exclusions 
		END as exlusions
, CASE WHEN extras is NULL or extras like '%null%' 
	THEN '' 
	ELSE extras 
		END as extras
into #customer_orders
FROM customer_orders;

SELECT order_id
, runner_id
, CASE 
    WHEN distance LIKE 'null' THEN ''
    WHEN distance LIKE '%km' THEN TRIM('km' from distance) 
    ELSE distance END AS distance
, CASE 
    WHEN pickup_time LIKE 'null' THEN ''
    ELSE pickup_time 
    END AS pickup_time
, CASE 
    WHEN duration LIKE 'null' THEN '' 
    WHEN duration LIKE '%mins' THEN TRIM('mins' from duration) 
    WHEN duration LIKE '%minute' THEN TRIM('minute' from duration)        
    WHEN duration LIKE '%minutes' THEN TRIM('minutes' from duration)       
    ELSE duration END AS duration
, CASE 
    WHEN cancellation IS NULL or cancellation LIKE 'null' THEN ''
    ELSE cancellation END AS cancellation
INTO #runner_orders
FROM runner_orders;

ALTER TABLE #runner_orders
--ALTER COLUMN pickup_time DATETIME,
--ALTER COLUMN distance float 
ALTER COLUMN duration INT;
```

### 1. How many pizzas were ordered?

```sql
SELECT 
COUNT (*) as ordered_pizza
FROM #customer_orders
```
![image](https://user-images.githubusercontent.com/80718915/152913500-7a80faf1-801f-4366-9798-5d5176b62c2b.png)

**Answer**: 14 pizzas were ordered

### 2. How many unique customer orders were made?

```sql 
SELECT 
COUNT (distinct order_id ) as ordered_pizza
FROM #customer_orders
```
![image](https://user-images.githubusercontent.com/80718915/152913627-5f10315b-ef7b-4d20-b518-d327ff5b933f.png)

**Answer**: 10 unique customer orders were made

### 3. How many successful orders were delivered by each runner?

```sql
SELECT 
runner_id
,count (order_id) as runner_count
FROM #runner_orders
where cancellation not like '%cancellation%'
group by runner_id
```
![image](https://user-images.githubusercontent.com/80718915/152913711-0f93b23a-2e6f-4ad1-8c8b-c2e192d2acc8.png)

**Answer**: Runner one delivered 4 pizzas successfully, Runner 2 delivered 3 pizzas successfully and Runner 3 delivered 1 pizzas successfully

### 4. How many of each type of pizza was delivered?

```sql
SELECT 
cast (p.pizza_name as NVARCHAR(100)) as pizza_name -- was having issues with the data type 
,count(p.pizza_id) as count
FROM #customer_orders c
join pizza_names p on p.pizza_id = c.pizza_id
join #runner_orders r on r.order_id = c.order_id
where cancellation not like '%cancellation%'
group by cast (p.pizza_name as NVARCHAR(100)); 
```
![image](https://user-images.githubusercontent.com/80718915/152913800-2d273a81-6c89-4728-bd14-7d86324a1b55.png)

**Answer**: 9 Meatlovers and 3 Vegetarians were delivered 

### 5. How many Vegetarian and Meatlovers were ordered by each customer?

```sql
SELECT 
customer_id
,cast(p.pizza_name as nvarchar(100)) as pizza_name
,count (cast(p.pizza_name as nvarchar(100))) as count
FROM #customer_orders c
join pizza_names p on c.pizza_id = p.pizza_id
group by customer_id, cast (p.pizza_name as NVARCHAR(100));
```
![image](https://user-images.githubusercontent.com/80718915/152913915-f786b9af-b8b6-446c-b340-90267b935d44.png)

**Answer**:
-- Customer 101 ordered 2 Meatlovers and 1 vegetarian 
-- Customer 102 ordered 2 Meatlovers and 1 vegetarian 
-- Customer 103 ordered 3 Meatlovers and 1 vegetarian 
-- Customer 104 ordered 3 Meatlovers 
-- Customer 105 ordered 1 vegetarian 

### 6. What was the maximum number of pizzas delivered in a single order?

```sql
-- DROP TABLE IF EXISTS #temp
SELECT 
c.order_id
, count(c.pizza_id) as pizza
into #temp
FROM #customer_orders c
join #runner_orders r on r.order_id = c.order_id
where cancellation not like '%cancellation%'
group by c.order_id;

SELECT 
 max(pizza) as count_pizza
FROM #temp
```
![image](https://user-images.githubusercontent.com/80718915/152914014-d260180c-dec7-4f75-94e9-37c7af9a982e.png)

**Answer**: The maximum number of pizza delivered in a single order was 3

--7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```sql
SELECT 
c.customer_id
,sum( case when c.exlusions != ' ' or c.extras != ' '  then 1 else 0 end)  as change
,sum (case when c.exlusions = ' ' or c.extras = ' ' then 1 else 0 end) as no_change
FROM #customer_orders c
join #runner_orders r on r.order_id = c.order_id
where r.cancellation not like '%cancellation%'
group by c.customer_id
order by c.customer_id
```
![image](https://user-images.githubusercontent.com/80718915/152914079-6adf251f-d757-402c-a9d1-71889a69c5bd.png)

**Answer**:
--Customer 101 and Cusomter 102 had no changes
-- Customer 103, 104 and 105 had atleast 1 change

### 8. How many pizzas were delivered that had both exclusions and extras?

```sql
SELECT count(* ) as pizza_exclu_extra
FROM #customer_orders c
join #runner_orders r on r.order_id = c.order_id
where c.exlusions != ' ' 
and c.extras !=  ' '
and duration != 0 
```
![image](https://user-images.githubusercontent.com/80718915/152914183-ec8979b9-55f7-4960-8255-c90b0ed3b334.png)

**Answer**: Only 1 pizza was delivered that had both exclusions and extras 

### 9. What was the total volume of pizzas ordered for each hour of the day?

```sql
SELECT 
 datepart (hour, [order_time]) as hour_of_day
, count(order_id) as count
FROM #customer_orders c
group by datepart (hour, [order_time]);
```
![image](https://user-images.githubusercontent.com/80718915/152914255-43f5f331-dc06-4bfc-8c20-91269a52d5ce.png)

**Answer**:
-- Hours: 11, 19 had 1 count of pizza ordered 
-- Hours: 13, 18, 21, 23 had 3 counts of pizzas ordered


### 10.What was the volume of orders for each day of the week?

```sql
SELECT 
 format(dateadd (day, 2, order_time), 'dddd') as week
, count(order_id) as count
FROM #customer_orders c
group by format(dateadd (day, 2,order_time), 'dddd');
```
![image](https://user-images.githubusercontent.com/80718915/152914447-e1716a9a-9cad-4730-9d8e-9f7ede156b4b.png)

**Answer**:
-- Friday and Monday had 5 orders of pizzas
-- Saturday had 3 orders of pizzas
-- Sunday had 1 order of pizza

