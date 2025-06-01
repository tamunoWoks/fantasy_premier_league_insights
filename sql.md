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
| | Winner | Total Winnings |
|-|:--------|:----------------|
| 1 | Wolfgang FC | N49,000 |
| 2 | Dandi CF | N41,000 |
| 3 | Bode United | N28,000 |
| 4 | FC Storm | N24,000 |
| 5 | Rich Boyz FC  | N21,000 |
| 6 | A.O.E. FC | N11,000 |
| 7 | Ogbonna FC | N7,000 |
| 8 | Pontus FC | N6,000 |
| 9 | Obarifiomi FC | N6,000 |
