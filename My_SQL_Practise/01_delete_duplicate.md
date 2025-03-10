
```sql
create database duplicated;
use duplicated;

--  ____________________remove_duplicate_data__________________
drop table cars;
create table if not exists cars
(
    id      int,
    model   varchar(50),
    brand   varchar(40),
    color   varchar(30),
    make    int
);
insert into cars values (1, 'Model S', 'Tesla', 'Blue', 2018);
insert into cars values (2, 'EQS', 'Mercedes-Benz', 'Black', 2022);
insert into cars values (3, 'iX', 'BMW', 'Red', 2022);
insert into cars values (4, 'Ioniq 5', 'Hyundai', 'White', 2021);
insert into cars values (5, 'Model S', 'Tesla', 'Silver', 2018);
insert into cars values (6, 'Ioniq 5', 'Hyundai', 'Green', 2021);

select * from cars;

-- Write a query to Delete duplicate data from cars table:
-- Duplicate record is identified based on the model and brand name

select * from cars order by model,brand;

-- __________________________XXXXXXXXX________________________
-- __________________________ Solution _______________________
-- __________________________XXXXXXXXX________________________


-- > SOLUTION 1: Delete using unique identifier.
-- > SOLUTION 2: Using self join.
-- > SOLUTION 3: Using window function.
-- > SOLUTION 4: Using backup table.

-- __________________________XXXXXXXXX________________________
-- _______________________ Solution ONE  ____________________
-- __________________________XXXXXXXXX________________________
select model,brand from cars;

-- We got the duplicate record base on model and brand column:
select model,brand,count(*) AS count -- if we don't to a specific name then we can skip AS count
from cars group by model,brand
having count >1;

-- By using max(id) we can get all the id of the duplicate record
select model,brand, max(id) 
from cars group by model,brand
having count(*) >1;

-- Now Delete all the rows, base on sub query
-- where id>123; (we need to give an single column to in clause)
-- SET SQL_SAFE_UPDATES = 0; (sometimes get safe-update error!)
DELETE FROM cars 
WHERE id IN (
    SELECT MAX(id) 
    FROM cars 
    GROUP BY model, brand 
    HAVING COUNT(*) > 1
);

-- __________________________XXXXXXXXX________________________
-- _______________________ Solution TWO  _____________________
-- _____________________ Using self join _____________________
-- __________________________XXXXXXXXX________________________
select * from cars;

-- using self join
-- from this query we will get all the value but we need only duplicated
-- give an onditin on id column: 
select * from cars as c1 
join cars as c2 
on c1.model=c2.model and c1.brand=c2.brand;

select * from cars as c1 
join cars as c2 
on c1.model=c2.model and c1.brand=c2.brand
where c1.id < c2.id;

-- now we can delete and we are interested in with only id column:
delete from cars where id in 
(select c1.id from cars as c1 
join cars as c2 
on c1.model=c2.model and c1.brand=c2.brand
where c1.id < c2.id);

-- __________________________XXXXXXXXX________________________
-- ______________________ Solution There  ____________________
-- __________________ Using Windows Function _________________
-- __________________________XXXXXXXXX________________________

-- we will get duplicated value as row number2
select *,
ROW_NUMBER() over(partition by model,brand) as "rn"
from cars;

-- get only the duplicate record
select *
from (select *,
ROW_NUMBER() over(partition by model,brand) as "rn"
from cars) x
where x.rn >1;

-- we only interest in: id(primary key)coulmn:
select id
from (select *,
ROW_NUMBER() over(partition by model,brand) as "rn"
from cars) x
where x.rn >1;

-- now delete form this:
delete from cars where id in(
select id
from (select *,
ROW_NUMBER() over(partition by model,brand) as "rn"
from cars) x
where x.rn >1);
```

