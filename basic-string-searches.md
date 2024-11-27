## Q5:  Basic string searches

>How can you produce a list of all facilities with the word 'Tennis' in their name? 

```sql
SELECT 
    * 
FROM 
    cd.facilities 
WHERE 
    name LIKE '%Tennis%'
;