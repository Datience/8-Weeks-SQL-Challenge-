# 📣📣 8 Weeks SQL Challenge

This repository contains the solution for the cases studies in the #8WeekSQLChallenge hosted by @DatawithDanny. 

*Working on one case study per week, if questions from a case study are not answered within a week, I will be coming back to those questions after working on all the 8 case challenges.*

***Status**: Due to other work prioritize, stepping away from these case studies for couple of weeks. I will be back to work on Case Study 5*

# 📖 Table of Contents 

• [Case Study #1: Danny's Diner](#case-study-1-dannys-diner)

• [Case Study #2: Pizza Runner](#case-study-2-pizza-runner)

• [Case Study #3: Food Fi](#case-study-3-food-fi)

• [Case Study #4: Data Bank](#case-study-4-data-bank)

• [Case Study #5: Data Mart](#case-study-5-data-mart)

## Case Study #1: Danny's Diner 

<img src="https://user-images.githubusercontent.com/81607668/127727503-9d9e7a25-93cb-4f95-8bd0-20b87cb4b459.png" alt="Image" width="500" height="520">

### Problem Statement 

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

### Entity Relationship Diagram & Dataset

Danny has shared 3 key datasets for this case study:

• sales

• menu

• members

<img src="https://user-images.githubusercontent.com/80718915/152446767-9f7a2447-e0ca-4bc2-8573-5cee008fcba1.png">

## Case Study #2: Pizza Runner

<img src ="https://user-images.githubusercontent.com/80718915/152455319-e894bc25-ca78-4f63-afbe-5d8a194b727d.png" alt="Image" width="500" height="520">

### Problem Statement 

Danny had a few years of experience as a data scientist - he was very aware that data collection was going to be critical for his business’ growth.

He has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.

### Entity Relationship Diagram & Dataset

Danny has shared 6 key datasets for this case study:

• Runners

• Customer order

• Runner order

• Pizza name

• Pizza recipe 

• Pizza toppings

<img src = "https://user-images.githubusercontent.com/80718915/152455634-42280069-4891-4a88-b6bd-5a6232de5c0f.png">

## Case Study #3: Food Fi 

<img src ="https://user-images.githubusercontent.com/80718915/154827251-c05f612d-e16d-4dda-bd4e-dac2fc867833.png" alt="Image" width="500" height="520">

### Problem Statement 

Danny finds a few smart friends to launch his new startup Foodie-Fi in 2020 and started selling monthly and annual subscriptions, giving their customers unlimited on-demand access to exclusive food videos from around the world!

Danny created Foodie-Fi with a data driven mindset and wanted to ensure all future investment decisions and new features were decided using data. This case study focuses on using subscription style digital data to answer important business questions.

### Entity Relationship Diagram & Dataset

Danny has shared 2 key datasets for this case study:

• Plans

• Subscriptions

![image](https://user-images.githubusercontent.com/80718915/154827267-b630a1eb-4c05-47f8-8df3-f089a1c6242d.png)

## Case Study #4: Data Bank

<img src ="https://user-images.githubusercontent.com/80718915/156437119-10f25bc6-3717-4ffa-8ecf-9845cc277291.png" alt="Image" width="500" height="520"> 

### Problem Statement 

There is a new innovation in the financial industry called Neo-Banks: new aged digital only banks without physical branches.

Danny thought that there should be some sort of intersection between these new age banks, cryptocurrency and the data world…so he decides to launch a new initiative - Data Bank!

Data Bank runs just like any other digital bank - but it isn’t only for banking activities, they also have the world’s most secure distributed data storage platform!

Customers are allocated cloud data storage limits which are directly linked to how much money they have in their accounts. There are a few interesting caveats that go with this business model, and this is where the Data Bank team need your help!

The management team at Data Bank want to increase their total customer base - but also need some help tracking just how much data storage their customers will need.

This case study is all about calculating metrics, growth and helping the business analyse their data in a smart way to better forecast and plan for their future developments!

### Entity Relationship Diagram & Dataset

Danny has shared 3 key datasets for this case study:

• Region

• Customer Nodes

• Customer Transactions

![image](https://user-images.githubusercontent.com/80718915/156437363-e5de7afa-f43b-4b1f-bb21-b74febb0d266.png)

## Case Study #5: Data Mart

<img src ="https://user-images.githubusercontent.com/80718915/158301611-6851922f-b0fe-4ac5-ad33-d271a9e19592.png" alt="Image" width="500" height="520"> 

### Problem Statement 

Data Mart is Danny’s latest venture and after running international operations for his online supermarket that specialises in fresh produce - Danny is asking for your support to analyse his sales performance.

In June 2020 - large scale supply changes were made at Data Mart. All Data Mart products now use sustainable packaging methods in every single step from the farm all the way to the customer.

Danny needs your help to quantify the impact of this change on the sales performance for Data Mart and it’s separate business areas.

The key business question he wants you to help him answer are the following:

1. What was the quantifiable impact of the changes introduced in June 2020?
2. Which platform, region, segment and customer types were the most impacted by this change?
3. What can we do about future introduction of similar sustainability updates to the business to minimise impact on sales?

### Entity Relationship Diagram & Dataset

Danny has shared 1 key datasets for this case study:

• data_mart.weekly_sales

![image](https://user-images.githubusercontent.com/80718915/158301733-1cd0a5aa-01dd-4ed0-ba88-158b23ce5bbc.png)

### Column Dictionary
The columns are pretty self-explanatory based on the column names but here are some further details about the dataset:

1. Data Mart has international operations using a multi-region strategy
2. Data Mart has both, a retail and online platform in the form of a Shopify store front to serve their customers
3. Customer segment and customer_type data relates to personal age and demographics information that is shared with Data Mart
4. transactions is the count of unique purchases made through Data Mart and sales is the actual dollar amount of purchases

Each record in the dataset is related to a specific aggregated slice of the underlying sales data rolled up into a week_date value which represents the start of the sales week.

