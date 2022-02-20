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

**Answer**: The pizza runner has made $138  so far 
