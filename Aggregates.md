## Q1: Count the number of facilities

> For our first foray into aggregates, we're going to stick to something simple. We want to know how many facilities exist - simply produce a total count. 

```sql
SELECT
	COUNT(*)
FROM 
    cd.facilities
;
```

