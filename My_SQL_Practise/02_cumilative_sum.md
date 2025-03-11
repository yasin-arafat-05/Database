
```sql
-- _______________________________________________________________________
-- ______________________ Problem Statement:______________________________
-- _______________________________________________________________________

-- Suppose you have a car travelling certain distance and the data is presented as follows -
-- Day 1 - 50 km
-- Day 2 - 100 km
-- Day 3 - 200 km

-- Now the distance is a cumulative sum as in
 --   row2 = (kms travelled on that day + row1 kms).

-- How should I get the table in the form of kms travelled by the car on a given day and not the sum of the total distance?

-- HINTS: Cumulative sum: we do in stat.

-- _______________________________________________________________________
-- _______________________________________________________________________
-- _________________________Sample Dataset:_______________________________
-- _______________________________________________________________________
-- _______________________________________________________________________
show DATABASES;
use duplicated;
drop table car_travels;
create table car_travels
(
    cars                    varchar(40),
    days                    varchar(10),
    cumulative_distance     int
);
insert into car_travels values ('Car1', 'Day1', 50);
insert into car_travels values ('Car1', 'Day2', 100);
insert into car_travels values ('Car1', 'Day3', 200);
insert into car_travels values ('Car2', 'Day1', 0);
insert into car_travels values ('Car3', 'Day1', 0);
insert into car_travels values ('Car3', 'Day2', 50);
insert into car_travels values ('Car3', 'Day3', 50);
insert into car_travels values ('Car3', 'Day4', 100);

select * from car_travels;


-- _______________________________________________________________________
-- _______________________________________________________________________
-- ___________________________ Solution: ________________________________
-- _______________________________________________________________________
-- _______________________________________________________________________
select * from car_travels;
-- lag(,no_of_previous_cell_check,default_value=0)

SELECT *, 
       (cumulative_distance - LAG(cumulative_distance) 
        OVER (PARTITION BY cars ORDER BY days)) AS distance_travel_per_day 
FROM car_travels;

-- ______________________ ERROR Handaling: _____________________________________ 
-- In MariaDB, the LAG() function does not support a default value (third parameter) like in MySQL or PostgreSQL
SELECT *, 
       (cumulative_distance - COALESCE(LAG(cumulative_distance, 1) 
        OVER (PARTITION BY cars ORDER BY days), 0)) AS distance_travel_per_day 
FROM car_travels;
SELECT VERSION();
```

