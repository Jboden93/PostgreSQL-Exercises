 ## Q6: Matching against multiple possible values

 >How can you retrieve the details of facilities with ID 1 and 5? Try to do it without using the OR operator. 

 ```sql
 SELECT 
    * 
FROM 
    cd.facilities
WHERE 
    facid IN (1,5)
;