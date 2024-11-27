## Q3: Control which rows are retrieved

>How can you produce a list of facilities that charge a fee to members? 

```sql
SELECT
    *
FROM
    cd.facilities
WHERE 
    membercost > 0