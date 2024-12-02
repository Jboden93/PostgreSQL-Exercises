## Q1: Count the number of facilities

> For our first foray into aggregates, we're going to stick to something simple. We want to know how many facilities exist - simply produce a total count. 

```sql
SELECT
	COUNT(*)
FROM 
    cd.facilities
;
```


## Q2: Count the number of expensive facilities

> Produce a count of the number of facilities that have a cost to guests of 10 or more. 

```sql
SELECT 
	COUNT(*)
FROM 
	cd.facilities
WHERE 
    guestcost >= 10 
;
```


## Q3: Count the number of recommendations each member makes.

> Produce a count of the number of recommendations each member has made. Order by member ID. 

```sql
SELECT
	recommendedby, 
	COUNT(memid)
FROM 
	cd.members
WHERE 
    recommendedby IS NOT NULL
GROUP BY
	recommendedby
ORDER BY 
    1 ASC
;
```


## Q4: List the total slots booked per facility

> Produce a list of the total number of slots booked per facility. For now, just produce an output table consisting of facility id and slots, sorted by facility id. 

```sql
SELECT 
	facid,
	SUM(slots) AS total_slots
FROM 
	cd.bookings
GROUP BY 
	facid
ORDER BY 
    facid
;
```


## Q5: List the total slots booked per facility in a given month

> Produce a list of the total number of slots booked per facility in the month of September 2012. Produce an output table consisting of facility id and slots, sorted by the number of slots. 

```sql
SELECT
	facid, 
	SUM(slots) AS total_slots
FROM 
	cd.bookings
WHERE 
    DATE_TRUNC('month', starttime) = '2012-09-01'
GROUP BY 
	facid
ORDER BY 
    total_slots
;
```


## Q6: List the total slots booked per facility per month

> Produce a list of the total number of slots booked per facility per month in the year of 2012. Produce an output table consisting of facility id and slots, sorted by the id and month. 

```sql
SELECT
	facid,
	DATE_PART('MONTH', starttime) AS month, 
	SUM(slots) AS total_slots
FROM 
	cd.bookings
WHERE
	DATE_PART('YEAR', starttime)::INTEGER = 2012
GROUP BY 
    facid, month
;
```

*Automatic implicit conversion of ```DATEPART()``` (Int -> DP). But explicitly casting provides clarity & bp.* 


## Q7: Find the count of members who have made at least one booking

> Find the total number of members (including guests) who have made at least one booking. 

```sql
SELECT 
	COUNT(DISTINCT memid)
FROM
	cd.bookings
;
```

*```COUNT(DISTINCT xxx)``` syntactic sugar (Otherwise would need to pass Distinct table).*


## Q8: List facilities with more than 1000 slots booked

> Produce a list of facilities with more than 1000 slots booked. Produce an output table consisting of facility id and slots, sorted by facility id. 

```sql
SELECT
    facid, 
    SUM(slots) total_slots
FROM 
    cd.bookings
GROUP BY
    facid
HAVING 
    SUM(slots) > 1000
ORDER BY 
    facid
;
```


## Q9: Find the total revenue of each facility

> Produce a list of facilities along with their total revenue. The output table should consist of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members! 

```sql
SELECT
	f.name, 
	SUM(CASE 
            WHEN b.memid = 0 THEN b.slots * guestcost
            WHEN b.memid != 0 THEN b.slots * membercost
            ELSE 0
	    END) AS revenue
FROM 
	cd.bookings AS b
	LEFT JOIN cd.facilities AS f ON b.facid = f.facid
GROUP BY 
    f.name
ORDER BY 
    revenue
;
```


## Q10:  Find facilities with a total revenue less than 1000

> Produce a list of facilities with a total revenue less than 1000. Produce an output table consisting of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members! 

#### Answer 1:
> CTE

```sql
WITH booking_revenue AS 
(
SELECT
	f.name, 
	SUM(CASE 
            WHEN b.memid = 0 THEN b.slots * guestcost
            WHEN b.memid != 0 THEN b.slots * membercost
            ELSE 0
	    END) AS revenue
FROM 
	cd.bookings AS b
	LEFT JOIN cd.facilities AS f ON b.facid = f.facid
GROUP BY 
    f.name
)

SELECT
	* 
FROM 
	booking_revenue
WHERE 
	revenue < 1000
ORDER BY 
    revenue
;
```

#### Answer 2:
> Subquery

```sql
SELECT
	* 
FROM
(
SELECT
	f.name, 
	SUM(CASE 
		WHEN b.memid = 0 THEN b.slots * guestcost
		WHEN b.memid != 0 THEN b.slots * membercost
		ELSE 0
	END) AS revenue
	FROM 
		cd.bookings AS b
	LEFT JOIN cd.facilities AS f ON b.facid = f.facid
	GROUP BY f.name
) AS booking_revenue
WHERE 
	revenue < 1000
ORDER 
	BY revenue
;
```


## Q11: Output the facility id that has the highest number of slots booked

> Output the facility id that has the highest number of slots booked. For bonus points, try a version without a LIMIT clause. This version will probably look messy! 

```sql
WITH total_slots AS 
(
SELECT
	facid, 
	SUM(slots) AS total_slots
FROM 
	cd.bookings
GROUP BY 
	facid
>>>>>>> cb4b0b6e144dbe717337403f53e16bc6891d43db
)

SELECT
	facid,
	total_slots
FROM 
	total_slots
WHERE 

	total_slots = (SELECT MAX(total_slots) FROM total_slots)
;
```


## Q12: List the total slots booked per facility per month, part 2

> Produce a list of the total number of slots booked per facility per month in the year of 2012. In this version, include output rows containing totals for all months per facility, and a total for all months for all facilities. The output table should consist of facility id, month and slots, sorted by the id and month. When calculating the aggregated values for all months and all facids, return null values in the month and facid columns.  

#### Answer 1:
> ```ROLLUP()```

```sql
SELECT 
	facid, 
	DATE_PART('month', starttime) AS month, 
	SUM(slots) AS total_slots
FROM 
	cd.bookings
WHERE 
	DATE_PART('year', starttime)::INTEGER = 2012
GROUP BY 
	ROLLUP(facid, month)
ORDER BY 
	facid, month
;
```

#### Answer 2:
> UNION ALL/Subqueries

```sql
SELECT 
	facid, 
	DATE_PART('month', starttime) AS month, 
	SUM(slots) AS total_slots
FROM 
	cd.bookings
WHERE 
	DATE_PART('year', starttime)::INTEGER = 2012
GROUP BY 
	facid, month

UNION ALL

SELECT 
	facid, 
	NULL, 
	SUM(slots) AS total_slots
FROM 
	cd.bookings
WHERE 
	DATE_PART('year', starttime)::INTEGER = 2012
GROUP BY 
	facid

UNION ALL

SELECT 
	NULL, 
	NULL, 
	SUM(slots) AS total_slots
FROM 
	cd.bookings
WHERE 
	DATE_PART('year', starttime)::INTEGER = 2012

ORDER BY 1, 2
;
```


## Q13: List the total hours booked per named facility

> Produce a list of the total number of hours booked per facility, remembering that a slot lasts half an hour. The output table should consist of the facility id, name, and hours booked, sorted by facility id. Try formatting the hours to two decimal places. 

```sql
SELECT
	f.facid, 
	f.name, 
	ROUND(SUM(b.slots) * 0.5, 2) AS total_slot_hours
FROM
	cd.bookings AS b
	JOIN cd.facilities AS f ON f.facid = b.facid
GROUP BY 
	f.facid, f.name
ORDER BY 
	f.facid, name
;
```


## Q14: List each member's first booking after September 1st 2012

> Produce a list of each member name, id, and their first booking after September 1st 2012. Order by member ID.

```sql
 SELECT
	m.surname, 
	m.firstname, 
	m.memid, 
	MIN(starttime) AS start_time
	
FROM
	cd.bookings AS b
	JOIN cd.members AS m ON m.memid = b.memid
WHERE 
	DATE(starttime) >= '2012-09-01' 
GROUP BY 
	m.surname, m.firstname,	m.memid 
ORDER BY 
	m.memid
;
```


## Q15: Produce a list of member names, with each row containing the total member count

> Produce a list of member names, with each row containing the total member count. Order by join date, and include guest members. 

#### Answer 1:
> Subquery

```sql
SELECT 
	(SELECT COUNT(*) FROM cd.members) AS total_count,
	firstname, 
	surname
FROM 
	cd.members
ORDER BY
	joindate
;
```

#### Answer 2:
> Window Function

```sql
SELECT 
	COUNT(*) OVER() AS total_count,
	firstname, 
	surname
FROM 
	cd.members
ORDER BY
	joindate
;
```

*Frame of unrestricted / partitioned WF = whole result set* 