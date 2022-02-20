Refer to Schema in Part I

--PART II. Runner and Customer Experience


### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

```sql
SELECT DATEPART(WEEK, registration_date) AS registration_week,
 COUNT(runner_id) AS runner_signup
FROM runners
GROUP BY DATEPART(WEEK, registration_date);
```
![image](https://user-images.githubusercontent.com/80718915/154826438-51d32d88-9945-4488-9315-9bd38ba0037c.png)

**Answer**: The first and third week of January had 1 runner signed up and the second week of January had 2 people signed up.


### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

```sql
SELECT concat (AVG(pickup_min),' minutes') as avg_time FROM (
 SELECT c.order_id
, c.order_time
, r.pickup_time
, DATEDIFF(MINUTE, c.order_time, r.pickup_time) AS pickup_min

FROM #customer_orders AS c
JOIN #runner_orders AS r ON c.order_id = r.order_id
WHERE r.cancellation not like '%cancellation%'
GROUP BY c.order_id, c.order_time, r.pickup_time)t;
```
![image](https://user-images.githubusercontent.com/80718915/154826459-3c5dbd2d-029a-450f-a268-62f32a77fa56.png)

**Answer**:  the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order was 16 minutes

### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

```sql 
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
```
![image](https://user-images.githubusercontent.com/80718915/154826483-ed3d16da-a2b6-43d1-a282-0a3a63ec228c.png)

**Answer**: One pizza takes 12 minutes to be prepared, 2 pizzas take 18 minutes in average to be prepared, and 3 pizzas take 30 minutes in average to be prepared.

### 4. What was the average distance travelled for each customer?

```sql
SELECT * FROM (
SELECT 
customer_id
, avg(distance) AS avg_distance
FROM #customer_orders c
JOIN #runner_orders r ON c.order_id = r.order_id
WHERE r.cancellation not like '%cancellation%'
GROUP BY customer_id)t
ORDER BY T.avg_distance; 
```
![image](https://user-images.githubusercontent.com/80718915/154826511-f3f919ea-8080-4efd-80a4-f1c19f4e9b6a.png)

**Answer**: The average distance of Customer 104 was the lowest of 10, Customer 102 - 16.73, Customer 101- 20, Customer 103 - 23.4, Customer 105 - 25 was the highest 

### 5. What was the difference between the longest and shortest delivery times for all orders?

```sql
SELECT 
MAX(cast(duration as int)) - MIN (cast(duration as int)) as delivery_diff
FROM #runner_orders
WHERE duration > 0 ; 
```
![image](https://user-images.githubusercontent.com/80718915/154826530-8d70a17b-fd85-4753-bbe3-b8e896fdacb9.png)

**Answer**:  The difference between the longest and shortest delivery times for all orders is 30 

### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

```sql
SELECT r.runner_id
, c.customer_id
, c.order_id
, ROUND((r.distance/r.duration * 60), 2) AS avg_speed
FROM #runner_orders r
JOIN #customer_orders  c ON r.order_id = c.order_id
WHERE  r.cancellation not like '%cancellation%'
GROUP BY r.runner_id, c.customer_id, c.order_id, r.distance, r.duration
ORDER BY c.order_id;
```
![image](https://user-images.githubusercontent.com/80718915/154826565-f45d9b2f-3ca2-4172-871b-df5ec64478ee.png)

**Answer**:
-- Runner 1’s average speed runs from 37.5 to 60
-- Runner 2’s average speed runs from 35.1 to 93.6
-- Runner 3’s average speed is 40


--7. What is the successful delivery percentage for each runner?

```sql
SELECT runner_id
, ROUND(100 * SUM
  (CASE WHEN distance = 0 THEN 0
  ELSE 1
  END) / COUNT(*), 0) AS delivery_per
FROM #runner_orders
GROUP BY runner_id;
```
![image](https://user-images.githubusercontent.com/80718915/154826582-f1a924c7-064a-4690-8dd5-e61296f43e27.png)

**Answer**:
-- Runner 1 was 100% successful
-- Runner 2 was 75% successful
-- Runner 3 was 50% successful
