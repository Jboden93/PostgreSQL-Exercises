## Q1: Produce a timestamp for 1 a.m. on the 31st of August 2012

> Produce a timestamp for 1 a.m. on the 31st of August 2012. 

#### Answer 1: 
> Universal SQL
```sql
SELECT
    '2012-08-31 01:00:00'::timestamp
;
```
*Can also CAST()*


#### Answer 2:
> Postgres specific
```sql
SELECT
    timestamp '2012-08-31 01:00:00'
;
```