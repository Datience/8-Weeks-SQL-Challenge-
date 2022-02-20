Refer to Schema in Part I

--PART II. Runner and Customer Experience

```sql
--1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

SELECT DATEPART(WEEK, registration_date) AS registration_week,
 COUNT(runner_id) AS runner_signup
FROM runners
GROUP BY DATEPART(WEEK, registration_date);
```

Answer: The first and third week of January had 1 runner signed up and the second week of January had 2 people signed up.


--2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

SELECT concat (AVG(pickup_min),' minutes') as avg_time FROM (
 SELECT c.order_id
, c.order_time
, r.pickup_time
, DATEDIFF(MINUTE, c.order_time, r.pickup_time) AS pickup_min

FROM #customer_orders AS c
JOIN #runner_orders AS r ON c.order_id = r.order_id
WHERE r.cancellation not like '%cancellation%'
GROUP BY c.order_id, c.order_time, r.pickup_time)t;

-- Answer:  the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order was 16 minutes

--3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

SELECT t.pizza_order
, avg(prep_time) as avg_preptime FROM (
SELECT 
c.order_id
, COUNT(c.order_id) AS pizza_order
, c.order_time
, r.pickup_time
, DATEDIFF(MINUTE, c.order_time, r.pickup_time) AS prep_time

FROM #customer_orders  c 
JOIN #runner_orders r ON c.order_id = r.order_id
WHERE r.cancellation not like '%cancellation%'
GROUP BY c.order_id, c.order_time, r.pickup_time) T
GROUP BY pizza_order

-- Answer: One pizza takes 12 minutes to be prepared, 2 pizzas take 18 minutes in average to be prepared, and 3 pizzas take 30 minutes in average to be prepared.

--4. What was the average distance travelled for each customer?

SELECT * FROM (
SELECT 
customer_id
, avg(distance) AS avg_distance
FROM #customer_orders c
JOIN #runner_orders r ON c.order_id = r.order_id
WHERE r.cancellation not like '%cancellation%'
GROUP BY customer_id)t
ORDER BY T.avg_distance; 

-- Answer: The average distance of Customer 104 was the lowest of 10, Customer 102 - 16.73, Customer 101- 20, Customer 103 - 23.4, Customer 105 - 25 was the highest 

--5. What was the difference between the longest and shortest delivery times for all orders?

SELECT 
MAX(cast(duration as int)) - MIN (cast(duration as int)) as delivery_diff
FROM #runner_orders
WHERE duration > 0 ; 

-- Answer:  The difference between the longest and shortest delivery times for all orders is 30 

--6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

SELECT r.runner_id
, c.customer_id
, c.order_id
, ROUND((r.distance/r.duration * 60), 2) AS avg_speed
FROM #runner_orders r
JOIN #customer_orders  c ON r.order_id = c.order_id
WHERE  r.cancellation not like '%cancellation%'
GROUP BY r.runner_id, c.customer_id, c.order_id, r.distance, r.duration
ORDER BY c.order_id;

-- Runner 1’s average speed runs from 37.5 to 60
-- Runner 2’s average speed runs from 35.1 to 93.6
-- Runner 3’s average speed is 40


--7. What is the successful delivery percentage for each runner?
SELECT runner_id
, ROUND(100 * SUM
  (CASE WHEN distance = 0 THEN 0
  ELSE 1
  END) / COUNT(*), 0) AS delivery_per
FROM #runner_orders
GROUP BY runner_id;

-- Runner 1 was 100% successful
-- Runner 2 was 75% successful
-- Runner 3 was 50% successful
