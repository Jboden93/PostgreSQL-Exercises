 ## Q10: Combining results from multiple queries

 > You, for some reason, want a combined list of all surnames and all facility names. Yes, this is a contrived example :-). Produce that list! 

 ```sql
 SELECT
	surname
FROM
	cd.members

UNION

SELECT
	name
FROM 
    cd.facilities
;
