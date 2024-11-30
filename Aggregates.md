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

