# Case Study #6 - Clique Bate

## Digital Analysis


#### 1️⃣ How many users are there?
```sql
SELECT 
	COUNT(distinct user_id) as user_count	
FROM clique_bait.users;
```
<img width="619" height="104" alt="image" src="https://github.com/user-attachments/assets/a247db90-6c89-49be-a5b9-b7906d4efd52" />


#### 2️⃣ How many cookies does each user have on average?
```sql
with cookies_count  AS (
  SELECT 
	user_id,
    COUNT(cookie_id) as cookie_count
  FROM clique_bait.users
  GROUP BY user_id)
 
SELECT ROUND(AVG(cookie_count), 0) avg_cookie_count
FROM cookies_count;
```
<img width="581" height="108" alt="image" src="https://github.com/user-attachments/assets/82e3c2d1-96ae-4f05-af4e-e0d2bd467baf" />


#### 3️⃣ What is the unique number of visits by all users per month?
```sql
SELECT 
   EXTRACT(MONTH FROM event_time::DATE) as months,
   COUNT(DISTINCT visit_id) as cookie_count
FROM clique_bait.events 
GROUP BY months
ORDER BY months ASC;
```
<img width="1184" height="274" alt="image" src="https://github.com/user-attachments/assets/6b310e6c-7c10-474d-a8a9-af04bc533c5d" />


#### 4️⃣ What is the number of events for each event type?
```
SELECT 
    event_type,
    COUNT(visit_id) as event_count

FROM clique_bait.events
GROUP BY event_type
ORDER BY event_type ASC;
```
<img width="1400" height="288" alt="image" src="https://github.com/user-attachments/assets/26612547-1285-4f01-8284-b1742314032c" />

#### 5️⃣ What is the percentage of visits which have a purchase event?
```
WITH event_counts as (
  SELECT 
    COUNT(DISTINCT e.visit_id) as event_count,
    COUNT(DISTINCT CASE WHEN event_name='Purchase' THEN e.visit_id END) as purchase_event_count
FROM clique_bait.events e
LEFT JOIN clique_bait.event_identifier ei on e.event_type= ei.event_type)

SELECT ROUND(100*purchase_event_count/event_count , 2) as purchase_percentage
FROM event_counts;
```
<img width="908" height="96" alt="image" src="https://github.com/user-attachments/assets/4658cddc-5628-44a3-a9bb-7df76ff6294a" />


#### 6️⃣ What is the percentage of visits which view the checkout page but do not have a purchase event?

#### 7️⃣ What are the top 3 pages by number of views?
```sql
SELECT 
	DISTINCT page_id,
	COUNT(visit_id) as visit_count
FROM clique_bait.events
GROUP BY page_id
ORDER BY visit_count DESC
LIMIT 3;
```
<img width="1313" height="196" alt="image" src="https://github.com/user-attachments/assets/faa20f7c-d585-4356-9158-8a0551f7e733" />

#### 8️⃣ What is the number of views and cart adds for each product category?
#### 9️⃣ What are the top 3 products by purchases?
