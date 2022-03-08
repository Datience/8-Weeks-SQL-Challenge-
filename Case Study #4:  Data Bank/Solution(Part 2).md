Refer to Schema in Part I

# B. Customer Transactions

### 1.What is the unique count and total amount for each transaction type?

```sql
SELECT 
c.txn_type
, count(*) as unique_count
, SUM(txn_amount) as total_amount
FROM customer_transactions c
group by c.txn_type;
```
![image](https://user-images.githubusercontent.com/80718915/157164680-7ce49757-935a-41e3-9b99-265d93dfa34b.png)

