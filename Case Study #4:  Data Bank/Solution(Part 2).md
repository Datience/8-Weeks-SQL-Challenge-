Refer to Schema in Part I

# B. Customer Transactions

### 1. What is the unique count and total amount for each transaction type?

```sql
SELECT 
c.txn_type
, count(*) as unique_count
, SUM(txn_amount) as total_amount
FROM customer_transactions c
GROUP BY c.txn_type;
```
![image](https://user-images.githubusercontent.com/80718915/157164680-7ce49757-935a-41e3-9b99-265d93dfa34b.png)

### 2. What is the average total historical deposit counts and amounts for all customers?

```sql 
SELECT 
ROUND (AVG(count_of_txn),0) avg_deposit
, CAST(AVG(cast (avg_deposit_amount as numeric)) AS DECIMAL (10,2)) avg_amount --CAST to get 2 decimal places 
FROM (
SELECT AVG(txn_amount) as avg_deposit_amount
,COUNT(*) count_of_txn
, customer_id
, txn_type
FROM (
SELECT * FROM customer_transactions
WHERE txn_type = 'deposit') t
GROUP BY customer_id, txn_type)S;
```
![image](https://user-images.githubusercontent.com/80718915/158296394-76e4c713-4b2b-4ff9-b6c7-5c815c2863a9.png)

### 3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?

```sql 
SELECT
  month,
  COUNT(DISTINCT customer_id) AS customer_count from (
   SELECT 
    customer_id, 
    month(txn_date) AS month,
    SUM(CASE WHEN txn_type = 'deposit' THEN 0 ELSE 1 END) AS deposit_count,
    SUM(CASE WHEN txn_type = 'purchase' THEN 0 ELSE 1 END) AS purchase_count,
    SUM(CASE WHEN txn_type = 'withdrawal' THEN 1 ELSE 0 END) AS withdrawal_count
  FROM customer_transactions
  GROUP BY customer_id, month(txn_date)
) t 
WHERE deposit_count >= 2 
  AND (purchase_count > 1 OR withdrawal_count > 1)
GROUP BY month
ORDER BY month;
```
![image](https://user-images.githubusercontent.com/80718915/158296480-bc321984-b270-4077-b5ba-a99a4860770b.png)

### 4. What is the closing balance for each customer at the end of the month?

*Come back to solve this*

### 5. What is the percentage of customers who increase their closing balance by more than 5%?

*Come back to solve this*

