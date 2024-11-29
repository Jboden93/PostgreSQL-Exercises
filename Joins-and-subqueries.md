## Retrieve the start times of members' bookings

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



