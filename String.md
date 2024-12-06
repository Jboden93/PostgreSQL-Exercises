## Q1: Format the names of members

> Output the names of all members, formatted as 'Surname, Firstname' 

```sql
SELECT
	CONCAT(surname, ', ', firstname)
FROM
	cd.members
;
```
*"||" standard SQL concat operator.*


## Q2: Find facilities by a name prefix

> Find all facilities whose name begins with 'Tennis'. Retrieve all columns. 

```sql
SELECT
	*
FROM 
	cd.facilities
WHERE
	name LIKE 'Tennis%'
;
```


## Q3: Perform a case-insensitive search

> Perform a case-insensitive search to find all facilities whose name begins with 'tennis'. Retrieve all columns. 

```sql
SELECT
	*	
FROM 
	cd.facilities
WHERE
	LOWER(name) LIKE 'tennis%'
;
```

*Postgres ILIKE() operator can perform case **in**sensitive matching.*


## Q4: Find telephone numbers with parentheses

> You've noticed that the club's member table has telephone numbers with very inconsistent formatting. You'd like to find all the telephone numbers that contain parentheses, returning the member ID and telephone number sorted by member ID. 

```sql
SELECT
	memid,
	telephone
FROM
	cd.members
WHERE
	telephone LIKE '%(%)%'
;
```

*"REGEXP" or "~" with '[()]' for regex.*


## Q5:  Pad zip codes with leading zeroes

> The zip codes in our example dataset have had leading zeroes removed from them by virtue of being stored as a numeric type. Retrieve all zip codes from the members table, padding any zip codes less than 5 characters long with leading zeroes. Order by the new zip code. 

```sql
SELECT 
	LPAD(zipcode::char(5), 5, '0')
FROM 
	cd.members
;
```