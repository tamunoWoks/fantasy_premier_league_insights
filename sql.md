**This file contains all questions asked of the dataset along side the sql queries used to return the results.**
---
- How many Gameweeks were available in total?
```sql
SELECT
  COUNT(*) AS gameweeks
FROM sfpl;
```
**Output= 38.**

---
- How many Gameweeks did participants stake?
```sql
SELECT
  COUNT(*) AS active_gameweeks
FROM sfpl
WHERE participants IS NOT NULL;
```
**Output= 35.**

---
- How much was shared among winners throughout entire season?
```sql
SELECT 
  SUM(prize) as total_prize_shared
FROM sfpl;
```
**Output= N193,000.**

---
- What is the total prize won by each team?  
```sql
SELECT 
    RANK() OVER (ORDER BY SUM("prize") DESC) AS "Rank",
    winner AS "Team",
    'N' || TO_CHAR(SUM("prize"), 'FM999,999') AS "Total Winnings"
FROM 
    sfpl
WHERE 
    winner IS NOT NULL
GROUP BY 
    winner
ORDER BY 
    SUM("prize") DESC;
```
**Output:**
| Rank | Team          | Total Winnings |
|------|---------------|----------------|
| 1    | Wolfgang FC   | N49,000        |
| 2    | Dandi CF      | N41,000        |
| 3    | Bode United   | N28,000        |
| 4    | FC Storm      | N24,000        |
| 5    | Rich Boyz FC  | N21,000        |
| 6    | A.O.E. FC     | N11,000        |
| 7    | Ogbonna FC    | N7,000         |
| 8    | Pontus FC     | N6,000         |
| 8    | Obarifiomi FC | N6,000         |

---
- Who won the highest weekly prize?
```sql
SELECT 
    gameweek,
    winner AS Team,
    'N' || TO_CHAR(prize, 'FM999,999') AS Prize_won
FROM 
   sfpl
WHERE 
    prize = (
        SELECT MAX(prize) 
        FROM sfpl
    )
LIMIT 1;
```
**Output:**
| Gameweek | Team        | Prize   |
|----------|-------------|---------|
| 17       | Wolfgang FC | N10,000 |

---
- Who won the highest weekly prize?
```sql
SELECT 
    gameweek,
    winner AS Team,
    'N' || TO_CHAR(prize, 'FM999,999') AS Prize_won
FROM 
   sfpl
WHERE 
    prize = (
        SELECT MIN(prize) 
        FROM sfpl
    )
LIMIT 1;
```
**Output:**
| Gameweek | Team        | Prize   |
|----------|-------------|---------|
| 11       | Dandi CF    | N3,000  |
---
- Team rankings according to weeks won:
```sql
SELECT 
    RANK() OVER (ORDER BY COUNT(winner) DESC) as rank,
    winner as team,
    COUNT(winner) as winning_weeks
FROM 
    sfpl
WHERE 
    winner IS NOT NULL
GROUP BY 
    winner
ORDER BY 
    COUNT(winner) DESC;
```
**Output:**
| Rank | Team          | Winning weeks |
|:------|:---------------|:---------------|
| 1    | Wolfgang FC   | 8             |
| 2    | Dandi CF      | 8             |
| 3    | FC Storm      | 5             |
| 4    | Bode United   | 5             |
| 5    | Rich Boyz FC  | 4             |
| 6    | A.O.E. FC     | 2             |
| 7    | Ogbonna FC    | 1             |
| 8    | Obarifiomi FC | 1             |
| 8    | Pontus FC     | 1             |
---
- What is the Average number of weekly stakers?
```sql
SELECT
	ROUND(AVG(participants), 0) as Avg_weekly_stakers
FROM sfpl
```
**Output= 6**
