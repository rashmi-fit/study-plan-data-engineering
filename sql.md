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

#  LAG
- access data from another row in your result set without using a self-join.
- looks at Previous row
- usecase : Compare current with earlier row

#  Lead
- access data from another row in your result set without using a self-join.
- looks at next row
- usecase : Compare current with next row

```
WITH input_data AS (
  SELECT 1 as ID , 'john@gmail.com 'as email
  UNION ALL
  SELECT 2 as ID , 'add@gmail.com 'as email
  UNION ALL
  SELECT 3 as ID , 'john@gmail.com 'as email
  UNION ALL
  SELECT 4 as ID , 'john@gmail.com 'as email
)
 
-- select * 
-- -- except(rn_num)
-- from (
--       select *,
--             row_number() over(partition by email order by id) as rn_num
--       from input_data
-- )
-- where rn_num > 1
```


# difference between row_number,rank(),dense_rank()
# how partition , order by works inside this

```
select  a.id, b.id ,a.email from input_data as a
inner join input_data as b
on a.email = b.email and a.id > b.id
```

# why do we see 4 twice but 3 once 

```
WITH marks as (
 
  select 'student1' as name , 80 as mark , 95 as attendance
  UNION ALL
  select 'student2' as name , 78 as mark , 99 as attendance
  UNION ALL
  select 'student3' as name , 75 as mark , 75 as attendance
  UNION ALL
  select 'student4' as name , 79 as mark , 75 as attendance
  UNION ALL
  select 'student5' as name , 80 as mark , 90 as attendance
  UNION ALL
  select 'student6' as name , 80 as mark , 90 as attendance
)

-- select * ,
--  row_number() over( order by mark desc ,name desc ) as rn_num 
--  ,rank() over( order by mark desc ,name desc  ) as rn_num_rank 
--  ,dense_rank() over( order by mark desc  ,name desc  ) as rn_num_dense
--  from marks
--  order by rn_num

select * from marks order by mark desc ,name desc
```
--  try by yourself
# for each class i want to see the ranking based on the mark
# for each class i want the ranking based on attendance and then mark 

```
WITH marks as (
 
  select 'class1' AS class, 'student1' as name , 80 as mark , 95 as attendance
  UNION ALL
  select 'class1' AS class, 'student2' as name , 78 as mark , 99 as attendance
  UNION ALL
  select 'class1' AS class, 'student3' as name , 75 as mark , 75 as attendance
  UNION ALL
  select 'class2' AS class, 'student4' as name , 79 as mark , 75 as attendance
  UNION ALL
  select 'class2' AS class, 'student5' as name , 80 as mark , 90 as attendance
  UNION ALL
  select 'class2' AS class, 'student6' as name , 80 as mark , 90 as attendance
  UNION ALL
  select 'class2' AS class, 'student7' as name , 75 as mark , 75 as attendance
)


select * from marks 
order by marks.name
limit 2
offset 2
```

# How to dedup the data 

```
with input_data as
(
          SELECT  
            '12345'                   AS sales_document
            , '1'                     AS sales_document_item
            , 200                     AS target_value_of_agreement
            , '2025-01-01'            AS record_created_at
            , 'EUR'                   AS currency      
            ,TIMESTAMP('2025-01-01')  AS ingestion_timestamp
          UNION ALL
          SELECT  
            '12345'                   AS sales_document
            , '2'                     AS sales_document_item
            , 300                     AS target_value_of_agreement
            , '2025-01-01'            AS record_created_at
            , 'EUR'                   AS currency      
            ,TIMESTAMP('2025-01-01')  AS ingestion_timestamp
          UNION ALL
          SELECT  
            '12345'                   AS sales_document
            , '1'                     AS sales_document_item
            , 500                     AS target_value_of_agreement
            , '2025-01-01'            AS record_created_at
            , 'EUR'                   AS currency      
            ,TIMESTAMP('2025-01-02')  AS ingestion_timestamp
),
raw_data as
( SELECT  
            '12345'                   AS sales_document
            , '1'                     AS sales_document_item
          -- UNION ALL
          -- SELECT  
          --   '12345'                   AS sales_document
          --   , '2'                     AS sales_document_item
)
 

-- select * from input_data
-- order by ingestion_timestamp

# get the latest values for each sales document and sales_document_item
,deduped_input as(
-- select * from(
select * 
from input_data as input
-- )where rn_num  =1
qualify (row_number() over(partition by sales_document, sales_document_item order by ingestion_timestamp desc )) = 1)

select * 
from deduped_input
inner join raw_data on 
raw_data.sales_document = deduped_input.sales_document and raw_data.sales_document_item = deduped_input.sales_document_item
```

```
WITH marks as (
 
  select 'class1' AS class, 'student1' as name , 80 as mark , 95 as attendance
  UNION ALL
  select 'class1' AS class, 'student2' as name , 78 as mark , 99 as attendance
  UNION ALL
  select 'class1' AS class, 'student3' as name , 75 as mark , 75 as attendance
  UNION ALL
  select 'class2' AS class, 'student4' as name , 79 as mark , 75 as attendance
  UNION ALL
  select 'class2' AS class, 'student5' as name , 80 as mark , 90 as attendance
  UNION ALL
  select 'class2' AS class, 'student6' as name , 80 as mark , 90 as attendance
  UNION ALL
  select 'class2' AS class, 'student7' as name , 75 as mark , 75 as attendance
)
--  try by yourself
-- for each class i want to see the ranking based on the mark
-- for each class i want the ranking based on attendance and then mark 
```

# learn about offset
# learn about except and except all, except distinct, union all, union distinct 

