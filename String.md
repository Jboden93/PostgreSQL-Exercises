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