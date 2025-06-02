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
- What week had the highest number of stakers, and who won that week?
```sql
SELECT 
    gameweek,
    winner AS Team,
    participants AS weekly_participants,
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
- What week had the lowest number of stakers, and who won that week?
```sql
SELECT 
    gameweek,
    winner AS Team,
    participants AS weekly_participants,
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
WHERE 
    prize IS NOT NULL;
```
**Output= 6**
---
- What is the Average amount of weekly stakes?
```sql
SELECT
    'N' || TO_CHAR(ROUND(AVG(prize)), 'FM999,999') as avg_weekly_stakes
FROM 
    sfpl
WHERE 
    prize IS NOT NULL;
```
**Output= N5,514**
---
- Who won the highest weekly points, in what week?
```sql
SELECT 
    gameweek,
    winner AS Team,
    winning_points AS Points_won
FROM 
   sfpl
WHERE 
    winning_points = (
        SELECT MAX(winning_points) 
        FROM sfpl
    )
LIMIT 1;
```
**Output:**

| Gameweek | Team        | Points won |
|:---------|:------------|:-----------|
| 24       | Bode United | 116        |
---
- Who won the lowest weekly points, in what week?
```sql
SELECT 
    gameweek,
    winner AS Team,
    winning_points AS Points_won
FROM 
   sfpl
WHERE 
    winning_points = (
        SELECT MIN(winning_points) 
        FROM sfpl
    )
LIMIT 1;
```
**Output:**
| Gameweek | Team        | Points won |
|:---------|:------------|:-----------|
| 11       | Dandi CF    | 37         |
---
- Count the number of distinct winners:
```sql
SELECT 
    COUNT(DISTINCT winner) AS distinct_winners_count
FROM 
    sfpl
WHERE 
    winner IS NOT NULL;
```
**Output= 9.**
---
- What is each team's highest prize win?
```sql
SELECT
    RANK() OVER (ORDER BY MAX(prize) DESC) AS rank,
    winner AS team,
    'N' || TO_CHAR(MAX(prize), 'FM999,999') AS highest_prize_won
FROM
    sfpl
WHERE
    winner IS NOT NULL
GROUP BY
    winner
ORDER BY
    MAX(prize) DESC;
```
| Rank | Team          | Highest Prize Won |
|:-----|:--------------|:------------------|
| 1    | Wolfgang FC   | N10,000           |
| 2    | Bode United   | N7,000            |
| 2    | Ogbonna FC    | N7,000            |
| 4    | Pontus Fc     | N6,000            |
| 4    | Dandi CF      | N6,000            |
| 4    | FC Storm      | N6,000            |
| 4    | Obariafomi FC | N6,000            |
| 4    | A.O.E. FC     | N6,000            |
| 4    | Rich Boyz FC  | N6,000            |
---
- Which teams had consecutive gameweek winnings?teams and their most consecutive prize winnings?
```sql
WITH numbered_games AS (
  SELECT 
    gameweek,
    winner,
    ROW_NUMBER() OVER (PARTITION BY winner ORDER BY gameweek) AS winner_seq,
    ROW_NUMBER() OVER (ORDER BY gameweek) AS overall_seq
  FROM 
    sfpl
  WHERE 
    winner IS NOT NULL
),
streak_groups AS (
  SELECT
    winner,
    (overall_seq - winner_seq) AS streak_id
  FROM
    numbered_games
),
streak_lengths AS (
  SELECT
    winner AS team,
    COUNT(*) AS streak_length
  FROM
    streak_groups
  GROUP BY
    winner, streak_id
  HAVING
    COUNT(*) >= 2  -- Only include streaks of 2+ wins
)
SELECT
  RANK() OVER (ORDER BY MAX(streak_length) DESC) AS rank,
  team,
  MAX(streak_length) AS longest_consecutive_wins
FROM
  streak_lengths
GROUP BY
  team
ORDER BY
  longest_consecutive_wins DESC, team;
```
| Rank | Team        | Consecutive wins |
|:-----|:------------|:-----------------|
| 1    | Dandi CF    | 3                |
| 2    | FC Storm    | 2                |
| 2    | Wolfgang FC | 2                |
---
