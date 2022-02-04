
# Solution to Case Studey #1 :Danny's Diner 

### 1. What is the total amount each customer spent at the restaurant?

```sql --
SELECT 
customer_id
,SUM (m.price) as total
FROM sales s
left join menu m on m.product_id = s.product_id
group by customer_id;

``` --

Answer: Customer A spent $76, Customer B spent $74 and Customer C spent $36
