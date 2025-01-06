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


## Q2: Find the downward recommendation chain for member ID 1

> Find the downward recommendation chain for member ID 1: that is, the members they recommended, the members those members recommended, and so on. Return member ID and name, and order by ascending member id. 

```sql
WITH RECURSIVE cte(members) AS
(
-- Anchor query to find all members with recommendedby = 1
SELECT 
	memid
FROM
	cd.members
WHERE
	recommendedby = 1

UNION ALL

-- recursive query to find memid where recommendedby = (1st instance anchor output, subsequent instances = prev recursive output)
SELECT
	memid
FROM 
	cd.members AS m1
	INNER JOIN cte ON cte.members = m1.recommendedby
)

-- joining another instance of the member table with an alias to bring in additional fields.
SELECT
	cte.members AS memid,
	m2.firstname AS firstname, 
	m2.surname AS surname
FROM 
	cte
	JOIN cd.members AS m2 ON cte.members = m2.memid
ORDER BY 
	memid
;
```

*correct answer advises ORDER BY memid (in final result set) but this would **confuse staggered 'recommended by' groupings??*** 


## Q3: Produce a CTE that can return the upward recommendation chain for any member

> Produce a CTE that can return the upward recommendation chain for any member. You should be able to select recommender from recommenders where member=x. Demonstrate it by getting the chains for members 12 and 22. Results table should have member and recommender, ordered by member ascending, recommender descending.

```sql
WITH RECURSIVE cte(member, recommender) AS
(
    -- Anchor Query: Start with recommender for memid 12 and 22 only.
    SELECT
  		memid,
        recommendedby
    FROM 
        cd.members
    WHERE 
        memid IN (12, 22)

    UNION ALL

    -- Recursive Query: Inner join 2nd instance of members table effectively restricting rows where memid = anchor recommender id. Repeating until null.
    SELECT
  		cte.member,
        m1.recommendedby
    FROM
        cd.members AS m1
    	INNER JOIN cte ON cte.recommender = m1.memid	
)

-- Final Select: Joining 3rd instance of member table to return all relevant recommender fields/details 
	cte.member,
	cte.recommender,
	m2.firstname,
	m2.surname
FROM 
	cte
	INNER JOIN cd.members AS m2 ON m2.memid = cte.recommender
ORDER BY
  		cte.member;
;
```

