## Q2: Control which rows are retrieved

>You want to print out a list of all of the facilities and their cost to members. How would you retrieve a list of only facility names and costs? 

```sql
SELECT 
    name, 
    membercost 
FROM 
    cd.facilities
;