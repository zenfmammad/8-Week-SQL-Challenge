# 🥑Case Study #3 - Foodie-Fi
## 🙋‍♀️ A. Customer Journey

#### 1️⃣ Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey. Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!

```sql
SELECT 
	customer_id, 
	plan_name,
    start_date,
    RANK() OVER(PARTITION BY customer_id ORDER BY start_date) as rank
FROM subscriptions s
LEFT JOIN plans p ON s.plan_id= p.plan_id
WHERE customer_id IN (1,2,3,4,5,6,7,8)
ORDER BY customer_id ASC;
```

![Screenshot 2025-04-26 161551](https://github.com/user-attachments/assets/6bf9c0f3-051e-490b-9558-888b399bdb35)

Based of the data of 8 customer above:

Customer 1️⃣:
Customer 1 started their journey with a 7-day free trial and then upgraded to the Basic Monthly plan on 2020-08-08.

![Screenshot 2025-04-26 162215](https://github.com/user-attachments/assets/6d7e14ed-d25b-452a-8664-91c8784e65bd)

Customer 2️⃣:
Customer 2 started their journey with a 7-day free trial and then upgraded to the Pro Annual plan on 2020-09-27

![Screenshot 2025-04-26 162633](https://github.com/user-attachments/assets/16b2876a-c594-44df-b0a8-287de16f2b3a)

Customer 4️⃣:
Customer 4 started with a 7-day free trial and upgraded to the Basic Monthly plan on 2020-01-24. About three months later, they churned and canceled their subscription.

![Screenshot 2025-04-26 162700](https://github.com/user-attachments/assets/647ea592-4938-466c-935a-118540b10446)



