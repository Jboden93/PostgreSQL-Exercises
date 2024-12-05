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


## Q2: Subtract timestamps from each other

> Find the result of subtracting the timestamp '2012-07-30 01:00:00' from the timestamp '2012-08-31 01:00:00' 

```sql
SELECT 
    CAST('2012-08-31 01:00:00' AS timestamp) - CAST('2012-07-30 01:00:00' AS timestamp) 
;
```


## Q3: Generate a list of all the dates in October 2012

> Produce a list of all the dates in October 2012. They can be output as a timestamp (with time set to midnight) or a date. 

```sql
SELECT 
	GENERATE_SERIES(date '2012-10-01', date '2012-10-31', interval '1 day') 
;
```