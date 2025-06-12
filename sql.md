| week1    |
| --------- | 
| leetcode problems https://leetcode.com/studyplan/top-sql-50/ | 

Notes:

How OVER (PARTITION BY ... ORDER BY ...) Works
PARTITION BY email: Groups rows by email (like a GROUP BY, but just for window functions).

ORDER BY id: Sorts each partition/group by id.

# ROW_NUMBER()
- Assigns a unique, sequential number to each row within a partition, starting from 1, ordered by the specified ORDER BY.
```
SELECT *,
       ROW_NUMBER() OVER(PARTITION BY email ORDER BY id) AS rn_num
FROM input_data;
```
#  RANK()
- Assigns a rank starting from 1 to each row within a partition, with gaps in case of ties. Tied rows get the same rank, and the next rank is skipped accordingly.
- Rank skips the value for every duplicate value found previously

# DENSE_RANK()
- Similar to RANK(), but without gaps in ranking. Tied rows get the same rank, and the next rank continues with no gaps.
- dense Rank will not skip the value for duplicate value found previously


Use cases:

- ROW_NUMBER() → Remove duplicates, keep the first.

- RANK() → Ranking with potential gaps (e.g., race positions).

- DENSE_RANK() → Ranking without gaps (e.g., assigning medals with ties).

