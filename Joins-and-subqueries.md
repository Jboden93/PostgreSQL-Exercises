## Q1: Retrieve the start times of members' bookings

> How can you produce a list of the start times for bookings by members named 'David Farrell'?  

```sql
SELECT 
	starttime
FROM 
	cd.bookings
JOIN 
	cd.members ON cd.members.memid = cd.bookings.memid
WHERE 
	CONCAT(firstname, ' ', surname) = 'David Farrell'
;
```

## Q2: Work out the start times of bookings for tennis courts

> How can you produce a list of the start times for bookings for tennis courts, for the date '2012-09-21'? Return a list of start time and facility name pairings, ordered by the time. 

```sql
SELECT
	b.starttime, 
	f.name
FROM 
	cd.bookings AS b
JOIN 
	cd.facilities AS f ON f.facid = b.facid
WHERE 
	f.name LIKE'%Tennis Court%' AND
	DATE(starttime) = '2012-09-21'
ORDER BY b.starttime
;
```


## Q3: Produce a list of all members who have recommended another member

> How can you output a list of all members who have recommended another member? Ensure that there are no duplicates in the list, and that results are ordered by (surname, firstname). 

```sql
SELECT DISTINCT
	er.firstname, 
	er.surname
FROM
	cd.members AS ee
JOIN 
	cd.members AS er ON er.memid = ee.recommendedby
ORDER BY 
	er.surname, er.firstname
;
```


## Q4: Produce a list of all members, along with their recommender

> How can you output a list of all members, including the individual who recommended them (if any)? Ensure that results are ordered by (surname, firstname). 

```sql
SELECT 
	m.firstname, 
	m.surname,
	r.firstname, 
	r.surname
FROM
	cd.members AS m
LEFT JOIN 
	cd.members AS r ON r.memid = m.recommendedby
ORDER BY
	m.surname, m.firstname 
;
```


## Q5: Produce a list of all members who have used a tennis court

> How can you produce a list of all members who have used a tennis court? Include in your output the name of the court, and the name of the member formatted as a single column. Ensure no duplicate data, and order by the member name followed by the facility name. 

*|| operator could be used to concat strings instead. **Caveat: does not work in MySQL or SQL Server***

```sql
SELECT DISTINCT 
	CONCAT(m.firstname, ' ', m.surname) AS member,
	f.name AS facility
FROM 
	cd.bookings AS b
JOIN 
	cd.facilities AS f ON f.facid = b.facid
JOIN
	cd.members AS m ON m.memid = b.memid AND
	F.name LIKE '%Tennis Court%'
ORDER BY 
    member ASC, facility ASC
;
```


## Q6: Produce a list of costly bookings

> How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost, and do not use any subqueries. 

```sql
SELECT
	CONCAT(m.firstname, ' ', m.surname) AS member, 
	f.name AS facility,
	CASE
		WHEN b.memid = 0 THEN b.slots * f.guestcost 
		WHEN b.memid != 0 THEN b.slots * f.membercost 
	END AS cost
FROM 
	cd.bookings AS b
INNER JOIN 
	cd.facilities AS f ON f.facid = b.facid
INNER JOIN 
    cd.members AS m ON m.memid = b.memid
WHERE 
	DATE(b.starttime) = '2012-09-14' AND
		((b.memid = 0 AND b.slots * f.guestcost > 30) OR (b.memid != 0 AND b.slots * f.membercost > 30))	
ORDER BY 
    cost DESC
;
```


## Q7: Produce a list of all members, along with their recommender, using no joins.

>How can you output a list of all members, including the individual who recommended them (if any), without using any joins? Ensure that there are no duplicates in the list, and that each firstname + surname pairing is formatted as a column and ordered. 

```sql
SELECT DISTINCT
	CONCAT(firstname, ' ', surname) AS member, 
	(SELECT 
		CONCAT(firstname, ' ', surname) 
	FROM cd.members AS r
	WHERE r.memid = m.recommendedby) AS recommender
FROM cd.members AS m
ORDER BY member ASC
;
```


## Q8: Produce a list of costly bookings, using a subquery

> The Produce a list of costly bookings exercise contained some messy logic: we had to calculate the booking cost in both the WHERE clause and the CASE statement. Try to simplify this calculation using subqueries. For reference, the question was:

How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost. 

```sql
SELECT
	name, 
	facility,
	cost
FROM
	(
	SELECT
		m.firstname || ' ' || m.surname AS name,
		f.name AS facility,
		CASE
			WHEN b.memid = 0 THEN b.slots * f.guestcost
			ELSE b.slots * f.membercost
		END AS cost
	FROM
		cd.bookings AS b
		JOIN cd.facilities AS f ON f.facid = b.facid
	  	JOIN cd.members AS m ON m.memid = b.memid
	WHERE 
		DATE(b.starttime) = '2012-09-14'
	) AS bookingcost
WHERE 
	cost > 30
ORDER BY
	cost DESC
;
```
