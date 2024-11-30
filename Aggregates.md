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