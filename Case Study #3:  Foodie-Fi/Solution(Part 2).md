Refer to Schema in Part I

# PART II. Data Analysis Questions

### 1. How many customers has Foodie-Fi ever had?

```sql
SELECT  
count(distinct customer_id) as count_customers
FROM subscriptions;
```

