
# Solution to Case Studey #1 :Danny's Diner 

### 1. What is the total amount each customer spent at the restaurant?

```sql 
SELECT 
customer_id
,SUM (m.price) as total
FROM sales s
left join menu m on m.product_id = s.product_id
group by customer_id;
```
![image](https://user-images.githubusercontent.com/80718915/152450993-b9d66580-32f6-4c57-823a-2b9fe9a1aacf.png)

**Answer**: Customer A spent $76, Customer B spent $74 and Customer C spent $36

### 2. How many days has each customer visited the restaurant?

```sql 
SELECT 
customer_id
, COUNT(DISTINCT (order_date)) as days
FROM sales s 
group by customer_id
```

**Answer**: Customer A visited 4 times, Customer B visited 6 times and Customer C visited 2 times


### 3. What was the first item from the menu purchased by each customer?

```sql
SELECT customer_id, product_name from (
SELECT 
customer_id
, m.product_id
, m.product_name
,dense_rank() OVER (partition BY s.customer_id order by s.order_date) as ranking 
-- the reason behind using dense_rank() over row_number and rank is because dense_rank assigns does not skip for similar values and it assigns the rank number to each row in a partition, in this case order.date
FROM sales s
LEFT JOIN menu m on m.product_id = s.product_id) t
WHERE t.ranking = 1
group by t.customer_id,  t.product_name;
```

**Answer**: Customer A ordered curry and sushi, Customer B ordered curry and Customer C ordered ramen for the first time from the menu
