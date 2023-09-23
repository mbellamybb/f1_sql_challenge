# 🏎️ Formula 1 SQL Challenge

Solving a Formula 1 challenge with SQL in Snowflake ❄️

<kbd>![Screenshot 2023-09-23 at 6 36 43 PM](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/b2a5d2cb-01fa-4954-89f8-d13ab975efab)<kbd>


## Table of Contents

- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Questions and Solutions](#questions-and-solutions)

***
## Entity Relationship Diagram

<kbd>![erd](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/c69128b8-b45f-47dc-918b-7726d9b421b2)<kbd>
***
## Questions and Solutions

**1. Return a list of F1 drivers (limit to 20 , sort by driverid)**

````sql
SELECT driverid,
        forename as "First Name",
        surname  as "Last Name"     
FROM drivers
ORDER BY driverid ASC
Limit 20;
````

#### Answer:
<kbd>![#1](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/c2143dad-e5c4-41cb-be4a-987faa5f6fc3)<kbd>

***

**2. Return the list of the top 10 drivers and their fastest lap speed (no aggregation)**

````sql
SELECT d.driverid,
        d.forename as "First Name",
        d.surname as "Last Name",
        ROUND(fastestlapspeed, 2) as "Fastest Lap Speed"
FROM drivers as d
JOIN results as r
    ON d.driverid = r.driverid
WHERE "Fastest Lap Speed" IS NOT NULL
ORDER BY "Fastest Lap Speed" DESC
LIMIT 10;
````

#### Answer:
<kbd>![#2](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/31549143-39b4-4251-9442-63102e549b99)<kbd>

***
**3. Return the list of the top 10 drivers and their fastest lap speed ever**

#### Query: 

````sql
SELECT d.driverid,
        d.forename as "First Name",
        d.surname as "Last Name",
        ROUND(MAX(r.fastestlapspeed), 2) as "Fastest Lap Speed Ever"
FROM drivers as d
JOIN results as r
    ON d.driverid = r.driverid
WHERE r.fastestlapspeed IS NOT NULL
GROUP BY d.driverid, "First Name", "Last Name"
ORDER BY "Fastest Lap Speed Ever" DESC
LIMIT 10;
````

#### Answer:
<kbd>![#3](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/1ca23859-a620-4144-b37e-952e07716e69)<kbd>

***

**4. Return the list of drivers and their fastest lap speed in Monaco in 2021**

#### Query: 

````sql
SELECT d.driverid,
        d.forename as "First Name",
        d.surname as "Last Name",
        ROUND(MAX(r.fastestlapspeed), 2) as "Fastest Lap Speed in Monaco"
FROM drivers as d
JOIN results as r
    ON d.driverid = r.driverid
JOIN races as ra
    ON r.raceid = ra.raceid
WHERE r.fastestlapspeed IS NOT NULL 
    AND ra.name LIKE '%Monaco%' 
    AND ra.year = 2021
GROUP BY d.driverid, "First Name", "Last Name"
ORDER BY "Fastest Lap Speed in Monaco" DESC;
````

#### Answer:
<kbd>![#4](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/558b48af-a5df-4909-8aac-1feaba4d958c)<kbd>

***
**5. Return the list of drivers  and fastest lap speed of all time (no JOINS)**

#### Query: 

````sql
SELECT forename as First_Name,
       surname as Last_Name,
        (SELECT MAX(fastestlapspeed) 
        FROM results) as All_Time_Fastest_Lap_Speed
FROM drivers
LIMIT 10;
````

#### Answer:
<kbd>![#5](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/89cf63d6-f401-4364-9895-d75e9831fd61)<kbd>

***
**6. Return the list of drivers and their fastest lap speed in Monaco in 2021, as well as the fastest lap speed of all time**

#### Query: 

````sql
SELECT  d.driverid
        forename,
        surname,
        ROUND(MAX(fastestlapspeed), 2) as Fastest_Lap_Speed_in_Monaco_2021,
        (SELECT MAX(fastestlapspeed)
            FROM results) as All_Time_Fastest_Lap_Speed
FROM drivers as d
JOIN results as r
    ON d.driverid = r.driverid
JOIN races as ra
    ON r.raceid = ra.raceid
WHERE r.fastestlapspeed IS NOT NULL 
    AND ra.name LIKE '%Monaco%' 
    AND ra.year = 2021
GROUP BY d.driverid,  forename, surname
ORDER BY Fastest_Lap_Speed_in_Monaco_2021 DESC;
````

#### Answer:
<kbd>![#6](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/cdd2cbfe-e0f6-4a09-a1a8-916366f7c2d7)<kbd>

***
**7. Return the list of drivers, their fastest lap speed in Monaco in 2021, as well as how many times they have won in Monaco in total**

#### Query: 


````sql
SELECT driverid,
        name,
        SUM(wins) as num_of_wins       
FROM driver_standings as ds
JOIN races as ra 
ON ra.raceid = ds.raceid
WHERE name LIKE '%Monaco%'
GROUP BY driverid, name
ORDER BY num_of_wins DESC
LIMIT 15;
````
<kbd>![#7a](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/8f0456a7-90e1-4d03-b202-c4688ab2870e)<kbd>

````sql
SELECT d.driverid, 
        forename, 
        surname, 
        w.num_of_wins,
        MAX(r.fastestlapspeed) as Fastest_Lap_Speed_in_Monaco_2021      
FROM drivers as d
JOIN results as r 
    ON d.driverid = r.driverid
JOIN races as ra 
    ON r.raceid = ra.raceid
JOIN (SELECT driverid,
                name,
                SUM(wins) as num_of_wins       
        FROM driver_standings as ds
        JOIN races as ra 
            ON ra.raceid = ds.raceid
        WHERE name LIKE '%Monaco%'
        GROUP BY driverid, name
        ORDER BY num_of_wins DESC) as w
ON d.driverid = w.driverid
WHERE fastestlapspeed IS NOT NULL
    AND ra.year = 2021
    AND ra.name LIKE '%Monaco%'
GROUP BY d.driverid, forename, surname, w.num_of_wins
ORDER BY Num_of_wins DESC, Fastest_Lap_Speed_in_Monaco_2021 DESC
LIMIT 15;
````

#### Answer:
<kbd>![#7b](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/43ef4b4e-2d4e-417d-9335-7796f08c70c1)<kbd>

***
**8. Return the list of drivers, their fastest lap speed in Monaco in 2021, as well as how many times they have won in Monaco before (use a CTE)**

#### Query: 

````sql
WITH Monaco_Wins as (SELECT driverid,
                            name,
                            SUM(wins) as num_of_wins       
                        FROM driver_standings as ds
                        JOIN races as ra 
                            ON ra.raceid = ds.raceid
                        WHERE name LIKE '%Monaco%'
                        GROUP BY driverid, name
                        ORDER BY num_of_wins DESC)
SELECT d.driverid, 
        d.forename, 
        d.surname, 
        mw.num_of_wins,
        MAX(r.fastestlapspeed) as Fastest_Lap_Speed_in_Monaco_2021      
FROM drivers as d
JOIN results as r 
    ON d.driverid = r.driverid
JOIN races as ra 
    ON r.raceid = ra.raceid
JOIN Monaco_Wins as mw
ON d.driverid = mw.driverid
WHERE fastestlapspeed IS NOT NULL
    AND ra.year = 2021
    AND ra.name LIKE '%Monaco%'
GROUP BY d.driverid, forename, surname, mw.num_of_wins
ORDER BY Num_of_wins DESC, Fastest_Lap_Speed_in_Monaco_2021 DESC;
````

#### Answer:
<kbd>![#8](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/f2766aec-0001-498f-999e-a36c67770fcd)<kbd>

***

**9.  Return the list of drivers, their fastest lap speeds in Monaco in 2021 and 2019 (use CTE)**

#### Query: 

````sql
WITH Monaco_2021 as (SELECT d.driverid,
                            d.forename,
                            d.surname,
                            MAX(r.fastestlapspeed) as Fastest_Lap_Speed_in_Monaco_2021
                        FROM drivers as d
                        JOIN results as r
                            ON d.driverid = r.driverid
                        JOIN races as ra
                            ON r.raceid = ra.raceid
                        WHERE r.fastestlapspeed IS NOT NULL 
                            AND ra.name LIKE '%Monaco%' 
                            AND ra.year = 2021
                        GROUP BY d.driverid, forename, surname
                        ORDER BY Fastest_Lap_Speed_in_Monaco_2021 DESC),
Monaco_2019 as (SELECT d.driverid,
                            d.forename,
                            d.surname,
                            MAX(r.fastestlapspeed) as Fastest_Lap_Speed_in_Monaco_2019
                        FROM drivers as d
                        JOIN results as r
                            ON d.driverid = r.driverid
                        JOIN races as ra
                            ON r.raceid = ra.raceid
                        WHERE r.fastestlapspeed IS NOT NULL 
                            AND ra.name LIKE '%Monaco%' 
                            AND ra.year = 2019
                        GROUP BY d.driverid, forename, surname
                        ORDER BY Fastest_Lap_Speed_in_Monaco_2019 DESC)
SELECT Monaco_2021.forename,
        Monaco_2021.surname,
        Fastest_Lap_Speed_in_Monaco_2019,
        Fastest_Lap_Speed_in_Monaco_2021
FROM Monaco_2021
JOIN Monaco_2019
ON Monaco_2019.driverid = Monaco_2021.driverid
ORDER BY  Fastest_Lap_Speed_in_Monaco_2019 DESC, Fastest_Lap_Speed_in_Monaco_2021 DESC;
````

#### Answer:
<kbd>![#9](https://github.com/mbellamybb/f1_sql_challenge/assets/95842597/68abe67a-88ba-4026-a435-70bb35f1504c)<kbd>




