
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
![image](https://user-images.githubusercontent.com/80718915/152452864-9dc0f3e8-0568-4a04-8980-8fb92a4f2cc1.png)

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
![image](https://user-images.githubusercontent.com/80718915/152452924-98963e30-159c-4770-b6ee-512ea79c7d88.png)

**Answer**: Customer A ordered curry and sushi, Customer B ordered curry and Customer C ordered ramen for the first time from the menu


### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

```sql
SELECT TOP 1
product_name
, count (product_name) as times
FROM sales s
inner JOIN menu m on m.product_id = s.product_id
group by product_name
order by times DESC
```
![image](https://user-images.githubusercontent.com/80718915/152453052-116b6bb1-3db6-4e30-83e5-c8ddc83817cd.png)

**Answer**: Ramen was the most purchased item on the menu and was purchased 8 times by all customers.

### 5. Which item was the most popular for each customer?

```sql
SELECT * FROM(
SELECT 
customer_id
, product_name
, COUNT(m.product_name) as popular_count
, DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY COUNT(s.customer_id) DESC) AS rank
FROM MENU m
JOIN sales s on s.product_id = m.product_id
group by customer_id, product_name)t
WHERE t.rank = 1
```

![image](https://user-images.githubusercontent.com/80718915/152453286-ff413e83-f241-4766-95d1-eb5fec59068a.png)

**Answer**: Customer A most popular item was Ramen, Customer B most popular item was Ramen, curry and sushi, Customer C most popular item was Ramen

### 6. Which item was purchased first by the customer after they became a member?

```sql
SELECT customer_id, product_name FROM (
SELECT 
s.customer_id
, mb.join_date
, s.order_date
, s.product_id
, m.product_name
, DENSE_RANK() over(partition by s.customer_id order by s.order_date) as rk
FROM sales s
join menu m on m.product_id = s.product_id
join members mb on mb.customer_id = s.customer_id
WHERE s.order_date >= mb.join_date)t 
WHERE t.rk = 1;
```
**Answer**: Customer A ordered curry and Customer B order sushi after they became a member. 

### 7. Which item was purchased just before the customer became a member?

```sql
SELECT customer_id, product_name FROM (
SELECT 
s.customer_id
, mb.join_date
, s.order_date
, s.product_id
, m.product_name
, DENSE_RANK() over(partition by s.customer_id order by s.order_date) as rk
FROM sales s
join menu m on m.product_id = s.product_id
join members mb on mb.customer_id = s.customer_id
WHERE s.order_date < mb.join_date)t
WHERE t.rk = 1;
```
![image](https://user-images.githubusercontent.com/80718915/152453707-e9e5f370-e4de-466a-92de-b078d0b8cba2.png)

**Answer**: Customer A ordered both sushi & curry and Customer B ordered curry before they both became members. 

### 8. What is the total items and amount spent for each member before they became a member?

```sql
SELECT 
s.customer_id
,count(distinct m.product_name) as total_product
,sum (m.price ) as total_price
FROM sales s
JOIN menu m on m.product_id = s.product_id
JOIN members mb on mb.customer_id = s.customer_id
WHERE s.order_date < mb.join_date
group by s.customer_id;
```
![image](https://user-images.githubusercontent.com/80718915/152453804-07d94335-421e-4e96-9156-aa128e88487b.png)

**Answer**: Customer A purchased 2 items in total and spent $25 before becoming a member. Customer B purchased 2 items in total and spent $40 before becoming a member. 

-- 9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
-- sushi has 20 points for each $ spent
```sql
SELECT customer_id, sum(points) as points from(
SELECT 
customer_id
, case when product_name = 'sushi' then m.price * 20
		else m.price * 10 
		end as points 
FROM sales s
JOIN menu m on m.product_id = s.product_id) t 
group by customer_id
```
![image](https://user-images.githubusercontent.com/80718915/152453870-aaf21e5d-53ab-449e-91e7-f473555189d1.png)

**Answer**: Customer A has 860 points, Customer B has 940 points, Customer C has 360 points


### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi. How many points do customer A and B have at the end of January?

```sql
-- DROP TABLE IF EXISTS #temp
-- DROP TABLE IF EXISTS #final
SELECT 
 s.customer_id as customer 
, order_date
, product_name
, join_date
, price
, DATEADD(DAY, 6, join_date) AS firstweek_date
, EOMONTH('2021-01-31') AS end_date
into #temp
FROM sales s
join menu m on m.product_id = s.product_id
join members mb on mb.customer_id = s.customer_id

SELECT * 
,SUM (CASE WHEN product_name = 'sushi' THEN 2 * 10 * price 
           WHEN order_date BETWEEN join_date AND firstweek_date THEN 2 * 10 * price
		   ELSE 10 * price END ) AS points
INTO #final
FROM #temp
WHERE order_date < end_date
group by customer, order_date, join_date, end_date, product_name, price, firstweek_date

SELECT 
customer as Customer
, SUM(points) as points
FROM #final
group by customer;
```
![image](https://user-images.githubusercontent.com/80718915/152454026-780b4706-f2a5-4078-9591-845d8de70de9.png)

**Answer**: customer A has 1370 points and Customer B had 820 points at the end of January
