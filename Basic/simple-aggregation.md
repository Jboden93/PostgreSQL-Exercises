## Q11: Simple aggregation

> You'd like to get the signup date of your last member. How can you retrieve this information?

```sql
SELECT 
	MAX(joindate) AS "latest_join_datetime"
FROM
	cd.members
;
