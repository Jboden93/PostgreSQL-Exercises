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


## Q7: Work out the number of days remaining in the month

> For any given timestamp, work out the number of days remaining in the month. The current day should count as a whole day, regardless of the time. Use '2012-02-11 01:00:00' as an example timestamp for the purposes of making the answer. Format the output as a single interval value. 

```sql
SELECT 
    (DATE_TRUNC('month', eom) + interval '1 month') - DATE(eom)  
FROM
	(SELECT 
        timestamp '2012-02-11 01:00:00' AS eom) AS tssq
;
```


## Q8: Work out the end time of bookings

> Return a list of the start and end time of the last 10 bookings (ordered by the time at which they end, followed by the time at which they start) in the system. 

```sql
SELECT
	starttime, 
	starttime + slots * interval '30 minutes' AS endtime
FROM 
	cd.bookings
ORDER BY 
    endtime DESC, starttime DESC 
LIMIT 
    10
;
```
*Caveat: Last 10 bookings = Tiered priority sort (latest ending > latest start) =/= latest start. LIMIT; therefore ignores potential duplicate cases.*


## Q9: Return a count of bookings for each month

> Return a count of bookings for each month, sorted by month  

```sql
SELECT
	DATE_TRUNC('months', starttime) as month,
	COUNT(*)
FROM 
	cd.bookings
GROUP BY 
	DATE_TRUNC('months', starttime)
ORDER BY month ASC
;
```


## Q10: Work out the utilisation percentage for each facility by month

> Work out the utilisation percentage for each facility by month, sorted by name and month, rounded to 1 decimal place. Opening time is 8am, closing time is 8.30pm. You can treat every month as a full month, regardless of if there were some dates the club was not open. 

```sql
WITH cte AS
(
SELECT	
    f.name AS name,
    DATE_TRUNC('months', starttime) AS month,
    SUM(b.slots) AS actual_slots

FROM
    cd.bookings AS b
    JOIN cd.facilities AS f ON f.facid = b.facid
GROUP BY
    name, month
)

SELECT
	name,
	month, 
    -- actual slots / available slots (days in month * total daily slots) * 100 
	ROUND(actual_slots/(EXTRACT(DAY FROM ((month + INTERVAL '1 month') - month))*25)*100, 1) AS utilisation
FROM
	cte
ORDER BY
	name, month
;
```
