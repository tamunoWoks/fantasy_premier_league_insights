**This file contains all questions asked of the dataset along side the sql queries used to return the results.**
---
1. How many Gameweeks were available in total?
```sql
SELECT
  COUNT(*) AS gameweeks
FROM sfpl;
```
**Output= 38.**

---
2. How many Gameweeks did participants stake?
```sql
SELECT
  COUNT(*) AS active_gameweeks
FROM sfpl
WHERE participants IS NOT NULL;
```
**Output= 35.**

---
3. How much was shared among winners throughout entire season?
```sql
SELECT 
  SUM(prize) as total_prize_shared
FROM sfpl;
```
**Output= N193,000.**

---
4. What is the total prize won by each team?  
```sql
SELECT 
	winner, SUM(prize) as total_winnings
FROM sfpl
WHERE winner IS NOT NULL
GROUP BY 1
ORDER BY 2 DESC;
```
