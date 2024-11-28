## Q12: More aggregation

> You'd like to get the first and last name of the last member(s) who signed up - not just the date. How can you do that?

#### Answer 1:
> Subquery

```sql
SELECT 
	firstname, 
	surname, 
	joindate
FROM 
	cd.members
WHERE
	joindate = 
		(SELECT MAX(joindate)
		 FROM cd.members)
;
```
#### Answer 2:
> ORDER BY & LIMIT  
*Caveat: This approach will miss records if latest joindate is shared by multiple records.*

```sql
SELECT 
	firstname, 
	surname, 
	joindate
FROM 
	cd.members
ORDER BY 
	joindate DESC
LIMIT 
	1
;
```