Refer to Schema in Part I

# Part III. Ingredient Optimisation:

### 1. What are the standard ingredients for each pizza?

```sql
SELECT * --SplitData
into #newrecipes
FROM pizza_recipes
cross apply STRING_SPLIT (cast(toppings as nvarchar),',') ;

SELECT  
pr.pizza_name
,nr.value
FROM  pizza_names pr 
JOIN #newrecipes nr on nr.pizza_id = pr.pizza_id
```
![image](https://user-images.githubusercontent.com/80718915/154826970-3d30a6fc-28c5-45fe-bfd9-5ce9899f0052.png)

**Answer:** The table below shows the standard ingredients for each pizza. 


### 2. What was the most commonly added extra?

```sql
SELECT * --SplitData
into #newcustomer
FROM #customer_orders
cross apply STRING_SPLIT (cast(extras as nvarchar),',') ;

SELECT * TOP 1 
cast (topping_name as nvarchar) AS topping_name
, count (*) as count
FROM #newcustomer nc
JOIN pizza_toppings nr on nc.value = nr.topping_id
GROUP BY cast (topping_name as nvarchar)
ORDER BY  cast (topping_name as nvarchar) 
```
![image](https://user-images.githubusercontent.com/80718915/154827084-84bed54c-28cb-4530-99e1-2d70ae37dafa.png)

**Answer:** The most commonly added extra was Bacon and it was added 4 times 


### 3. What was the most common exclusion?

```sql
SELECT * --SplitData
into #newcustomer2
FROM #customer_orders
cross apply STRING_SPLIT (cast(exlusions as nvarchar),',') ;

SELECT TOP 1 
cast (topping_name as nvarchar) AS topping_name
, count (*) as count
FROM #newcustomer2 nc2
JOIN pizza_toppings nr on nc2.value = nr.topping_id
GROUP BY cast (topping_name as nvarchar)
ORDER BY  cast (topping_name as nvarchar) 
``` 
![image](https://user-images.githubusercontent.com/80718915/154827147-0466fe6d-1a4a-4058-84ba-7fbc9b94ee1f.png)

**Answer:** The most common exclusion is BBQ Sauce 
