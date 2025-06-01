**This file contains all questions asked of the dataset along side the sql queries used to return the results.**
---
1. How many Gameweeks were active in total?
```sql
SELECT
  COUNT(*) AS gameweeks
FROM sfpl;
```
**Output= 38.**

---
2. How many Gameweeks did participants stake?
