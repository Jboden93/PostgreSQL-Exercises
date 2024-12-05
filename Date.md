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


## Q4:  Get the day of the month from a timestamp

> Get the day of the month from the timestamp '2012-08-31' as an integer.

```sql
SELECT 
    EXTRACT(day FROM date '2012-08-31')::integer
;
```


## Q5: Work out the number of seconds between timestamps

> Work out the number of seconds between the timestamps '2012-08-31 01:00:00' and '2012-09-02 00:00:00' 

```sql
SELECT
    EXTRACT(epoch from(timestamp '2012-09-02 00:00:00' - timestamp '2012-08-31 01:00:00'))
;
```

*Caveat = Postgres specific - will need to either calc manually or use dialect relevant function / library.
**Epoch = ref point from which time is measured ("00:00:00 UTC on January 1, 1970"). Time since epoch is measured in seconds.***



## Q6: Work out the number of days in each month of 2012

> For each month of the year in 2012, output the number of days in that month. Format the output as an integer column containing the month of the year, and a second column containing an interval data type. 

```sql
SELECT 
    EXTRACT('month' FROM timestamps) AS month, 
    (timestamps + INTERVAL '1 month' - timestamps) AS days
FROM
	(SELECT 
        GENERATE_SERIES('2012-01-01', '2012-12-31', INTERVAL '1 month') AS timestamps) AS time_period
;
```