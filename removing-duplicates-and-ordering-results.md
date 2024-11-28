## Q9: Removing duplicates, and ordering results

> How can you produce an ordered list of the first 10 surnames in the members table? The list must not contain duplicates.


```sql
SELECT DISTINCT
	surname
FROM
	cd.members
ORDER BY
	surname ASC
LIMIT 
    10
;