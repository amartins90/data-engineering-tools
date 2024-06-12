# SQL

## WHERE and HAVING

The keyword WHERE is used to filter rows while HAVING is a filter applied in the groups, therefore WHERE and HAVING can coexists in the same query. HAVING comes after GROUP BY so the condition is applied after aggregation, on the other hand WHERE is before GROUP BY so the results are filtered before the aggregation.

Example
```sql
SELECT name, SUM(working_hour) AS "Total working hours"
FROM employees
GROUP BY name
HAVING SUM(working_hour) > 6;
```

## TUNING
Example - Reduce number of rows before JOIN
```sql
SELECT teams.conference,
    sub.*
FROM (
        SELECT players.school_name,
            COUNT(*) AS players
        FROM benn.college_football_players players
        GROUP BY 1
    ) sub
JOIN benn.college_football_teams teams
ON teams.school_name = sub.school_name
```

EXPLAIN at the beginning of a query will calculate the cost of executing that query.
COALESCE returns the first value which is not null.

This is an example of how to calculate median in MySQL.
```sql
SET @rowindex := -1;

SELECT ROUND(AVG(d.LAT_N), 4)
FROM (
    SELECT @rowindex := @rowindex + 1 AS row_index, LAT_N
    FROM STATION
    ORDER BY LAT_N
) AS d
WHERE d.row_index IN (FLOOR(@rowindex / 2), CEIL(@rowindex/2))
```
1. Create a variable (e.g. rowindex);
2. Select from a list indexed from 0 to n (row_index), and containing the values (LAT_N);
3. Get the middle value(s): number of records / 2, then use AVG to calculate their mean.

## JOINS
Inner Join: returns the matching records from both tables.
Left Join: returns all records from left table and matching records from right table.
Right Join: returns all records from right table and matching records from left table.
Full Join: returns the combination of Right and Left joins.
Cross Join: returns the cartesian product between the tables, all records from both sides combined.

## WINDOW FUNCTION
Server first apply the query, then apply the window function.

Example 1
```sql
SELECT duration_seconds,
    SUM(duration_seconds) OVER (ORDER BY start_time) AS running_total
FROM tutorial.dc_bikeshare_q1_2012
```

Example 2
```sql
SELECT start_terminal,
    duration_seconds,
    SUM(duration_seconds) OVER (PARTITION BY start_terminal ORDER BY start_time) AS running_total
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```
Example 3 - ROW_NUMBER();
```sql
SELECT start_terminal,
    start_time,
    duration_seconds,
    ROW_NUMBER() OVER (PARTITION BY start_terminal ORDER BY start_time) AS row_number
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

Example 4 - RANK(); or DENSE_RANK();
```sql
SELECT start_terminal,
       duration_seconds,
       RANK() OVER (PARTITION BY start_terminal ORDER BY start_time) AS rank
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
```

Using RANK() if lines 2 and 3 are identical rank would consider rank 2 for both and the next result would be 4. Example: row 2 - rank 2, row 3 rank 2, row 4 rank 4. DENSE_RANK() will not skip the sequence. Example: row 2 - rank 2, row 3 rank 2, row 4 rank 3.

Example 5 - NTILE();
```sql
SELECT start_terminal,
    duration_seconds,
    NTILE(4) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quartile,
    NTILE(5) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS quintile,
    NTILE(100) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
ORDER BY start_terminal, duration_seconds
```

Example 5 - LAG();
```sql
SELECT start_terminal,
    duration_seconds,
    duration_seconds -LAG(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS difference
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
ORDER BY start_terminal, duration_seconds
```

Example 6 - LEAD();
```sql
SELECT start_terminal,
       duration_seconds,
       LAG(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS lag,
       LEAD(duration_seconds, 1) OVER (PARTITION BY start_terminal ORDER BY duration_seconds) AS lead
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
ORDER BY start_terminal, duration_seconds
```

Example 7 - ALIAS
```sql
SELECT start_terminal,
    duration_seconds,
    NTILE(4) OVER ntile_window AS quartile,
    NTILE(5) OVER ntile_window AS quintile,
    NTILE(100) OVER ntile_window AS percentile
FROM tutorial.dc_bikeshare_q1_2012
WHERE start_time < '2012-01-08'
WINDOW ntile_window AS (PARTITION BY start_terminal ORDER BY duration_seconds)
ORDER BY start_terminal, duration_seconds
```