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
