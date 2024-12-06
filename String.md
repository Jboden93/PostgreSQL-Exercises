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