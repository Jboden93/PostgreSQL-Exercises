## Q1:  Find the upward recommendation chain for member ID 27

> Find the upward recommendation chain for member ID 27: that is, the member who recommended them, and the member who recommended that member, and so on. Return member ID, first name, and surname. Order by descending member id. 

```sql
WITH RECURSIVE cte(recommender) AS
(
-- anchor query; producing base set results.
SELECT 
	recommendedby 
FROM 
	cd.members
WHERE
	memid = 27

UNION ALL

-- recursive query; initially using Anchor query results, any subsequent interations use prev recursive result.

SELECT
	m1.recommendedby
FROM 
	cd.members AS m1
	INNER JOIN cte ON m1.memid = cte.recommender
)

-- Joining another cd.members table to retrieve recommender related fields & selecting specific fields to match required output only.
SELECT 
	cte.recommender,
	m2.firstname, 
	m2.surname
FROM 
	cte
	INNER JOIN cd.members AS m2 ON m2.memid = cte.recommender
```
*Initially, the recursive query produces a 'recommender' chain result set. Then, the final join expands this result set to include additional 'recommender' details (firstname and surname), by joining with the cd.members table a second time.*