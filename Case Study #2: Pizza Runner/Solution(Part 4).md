Refer to Schema in Part I

# Part IV: Pricing and Ratings

```sql
CREATE TABLE pizza_price ("pizza_id" INTEGER, "price" INTEGER);
INSERT INTO pizza_price ("pizza_id", "price")
VALUES (1, 12),
    (2, 10);
```

### 1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes how much money has Pizza Runner made so far if there are no delivery fees?

```sql
SELECT  SUM(pizza_revenues)  total_revenue FROM (
SELECT C.pizza_id,
        COUNT(C.pizza_id) * price as pizza_revenues
    FROM #customer_orders AS c
        LEFT JOIN #runner_orders r  ON c.order_id = r.order_id
        LEFT JOIN pizza_price p ON c.pizza_id = p.pizza_id
    WHERE  r.cancellation not like '%cancellation%'
    GROUP BY C.pizza_id, price)t 
```
![image](https://user-images.githubusercontent.com/80718915/154827194-f75e05e2-2b76-4a8e-96df-fc487dc9b9c4.png)

**Answer**: The pizza runner has made $138  so far. 

### 2. What if there was an additional $1 charge for any pizza extras?
	--Add cheese is $1 extra

### 3. The Pizza Runner team now wants to add an additional ratings system that allows customers to rate their runner, 
--how would you design an additional table for this new dataset - generate a schema for this new table and insert your own data for 
--ratings for each successful customer order between 1 to 5.

### 4. Using your newly generated table - can you join all of the information together to form a table which has the 
--following information for successful deliveries?
	--customer_id
	--order_id
	--runner_id
	--rating
	--order_time
	--pickup_time
	--Time between order and pickup
	--Delivery duration
	--Average speed
	--Total number of pizzas

### 5. If a Meat Lovers pizza was $12 and Vegetarian $10 fixed prices with no cost for extras and each runner is paid $0.30 per kilometre traveled 
-- how much money does Pizza Runner have left over after these deliveries?
