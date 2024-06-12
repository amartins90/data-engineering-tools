# PostgreSQL examples

## CASE
```
SELECT
    member, facility, cost
FROM (
    SELECT
        CONCAT(m.firstname, ' ', m.surname) AS member,
	    f.name AS facility,
	    CASE WHEN
		    b.memid = 0 THEN f.guestcost * b.slots
	    ELSE
		    f.membercost * b.slots
	    END AS cost
    FROM
        cd.bookings b 
    INNER JOIN
        cd.facilities f ON b.facid = f.facid
    INNER JOIN
        cd.members m ON b.memid = m.memid
    WHERE b.starttime >= '2012-09-14' AND b.starttime < '2012-09-15'
    ) AS bookings
WHERE
    cost > 30
ORDER BY
    cost DESC
```

## RANK
```
SELECT
    RANK() OVER (ORDER BY joindate)
    row_number, firstname, surname
FROM cd.members
```
```
SELECT 
    facid, total
FROM (
    SELECT 
        b.facid, SUM(b.slots) AS total,
        RANK () OVER (ORDER BY SUM(b.slots) DESC)
    FROM cd.bookings b 
    INNER JOIN cd.facilities f ON b.facid = f.facid 
    GROUP BY
        b.facid
    ) AS ranking
WHERE
    rank = 1
```

## GENERATE SERIES
```
SELECT
	dategen.date,
	(
		SELECT SUM(b.slots *
				   	CASE WHEN b.memid = 0 THEN f.guestcost
				   	ELSE f.membercost END
				   ) AS rev
	  	FROM cd.bookings b
	  	INNER JOIN cd.facilities f
	  	ON b.facid = f.facid
	  	WHERE b.starttime > dategen.date - interval '14 days'
	  	AND b.starttime < dategen.date + interval '1 day'
	)/15 AS revenue
FROM 
	(
	  SELECT 
	  	CAST(GENERATE_SERIES(TIMESTAMP '2012-08-01', '2012-08-31', '1 day') AS date) AS date
	) AS dategen
ORDER BY dategen.date
```

## DATES
```
SELECT TIMESTAMP '2012-08-31 01:00:00' - TIMESTAMP '2012-07-30 01:00:00' AS INTERVAL;
```
```
SELECT DATE_PART('day', TIMESTAMP '2012-08-31');
```
```
SELECT
    EXTRACT(month FROM cal.month) AS month, (cal.month + '1 month') - cal.month AS length
FROM
(
    SELECT GENERATE_SERIES(TIMESTAMP '2012-01-01', '2012-12-31', '1 month') AS month
) cal
ORDER BY month;
```
```
SELECT
    DATE_TRUNC('month', starttime) AS month, COUNT(facid)
FROM
    cd.bookings
GROUP BY
    month
ORDER BY
    month
```

## STRINGS
```
SELECT memid, telephone FROM cd.members WHERE telephone ~ '[()]';
```
```
SELECT LPAD(zipcode::text, 5, '0') FROM cd.members;
```
```
SELECT
    memid, translate(telephone, '-() ', '') AS telephone
FROM
    cd.members
ORDER BY
    memid
```

## RECURSIVE
```
WITH RECURSIVE rec_chain(recommender) AS 
(
  SELECT recommendedby FROM cd.members WHERE memid = 27
  UNION ALL
  SELECT m.recommendedby FROM rec_chain rc INNER JOIN cd.members m
  ON rc.recommender = m.memid
)
SELECT rc.recommender, m.firstname, m.surname FROM rec_chain rc INNER JOIN cd.members m
ON rc.recommender = m.memid
ORDER BY m.memid DESC
```
```
WITH RECURSIVE rec_chain(member) AS
(
  SELECT memid FROM cd.members WHERE recommendedby = 1
  UNION ALL
  SELECT m.memid FROM rec_chain rc INNER JOIN cd.members m
  ON rc.member = m.recommendedby
)
SELECT rc.member, m.firstname, m.surname FROM rec_chain rc INNER JOIN cd.members m
ON rc.member = m.memid ORDER BY member
```
```
WITH RECURSIVE recommenders(member, recommender) AS
(
	SELECT memid, recommendedby FROM cd.members 
  	UNION ALL
  	SELECT r.member, m.recommendedby FROM recommenders r INNER JOIN cd.members m
  	ON m.memid = r.recommender
)
SELECT r.member, r.recommender, m.firstname, m.surname
FROM recommenders r INNER JOIN cd.members m
ON r.recommender = m.memid WHERE r.member IN (12, 22)
ORDER BY member ASC, recommender DESC
```