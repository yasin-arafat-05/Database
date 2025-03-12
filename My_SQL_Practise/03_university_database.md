
```sql
use university;

show tables; 
select * from department;

-- ________________________________________________________________________
-- ________________________________________________________________________
-- __________________________ PROBLEM SET:01 ______________________________
-- ________________________________________________________________________
-- ________________________________________________________________________

-- question no: 01
select dept_name from department where 
budget > (select budget from department where dept_name='Astronomy') 
order by dept_name ASC;

-- question no: 02
select * from instructor;
select * from teaches;

select I.ID,count(T.course_id) as num from 
instructor I left join teaches T
on I.ID = T.ID
GROUP BY I.ID;

-- question 03 (pari nai):
-- first group then, count>2;
select distinct course_id,ID from takes GROUP BY ID,course_id 
HAVING count(*)>2;

-- question 04 (pari nai):
-- accounting course koto jon nise
-- tar modde 2 ber beci nise ke ke
-- tar por student id :

select * from student;
select * from course;
select course_id from course
		where dept_name = "Accounting";
        
select name from student Where ID IN	
(select ID from takes where takes.course_id IN(select course_id from course
where dept_name = "Accounting")
group by ID having COUNT(*)>2)  AND dept_name = 'Biology';

-- question No(6):

-- ________________________________________________________________________
-- ________________________________________________________________________
-- __________________________ PROBLEM SET:02 ______________________________
-- ________________________________________________________________________
-- ________________________________________________________________________

-- question: 01
select ID,salary from instructor;

-- question: 02
select ID,salary from instructor where salary>85000;

-- question: 03
select dept_name,budget from department;

-- question: 04
select name from instructor where salary>70000 AND dept_name='Comp. Sci.';

-- question: 05 (pari nai)
select I.name, T.course_id from instructor I
natural join teaches T order BY I.name ASC;

-- question: 06(question bujte pari nai)
select * from instructor;
select min(salary) from instructor where dept_name='Biology';

select name from instructor where salary>
(select min(salary) from instructor where dept_name='Biology') and dept_name='Biology';

-- question: 07
select * from 
student S 
natural join advisor A
where A.i_id=12345;

-- question: 08 
select avg(salary) AS AVERATE_SALARY from instructor;

-- question: 09
select dept_name from department where building='Watson';

-- question: 10
select name from instructor where salary>90000 AND salary<100000;

-- question: 11
select I.name,T.course_id from instructor I natural join 
teaches T where dept_name='Biology';

-- question: 12 (question porte vul korci)
select course_id from teaches where semester='Fall' AND year='2009';

-- question:13 (pari nai)
select course_id from teaches where (semester='Fall' AND year='2009')
OR (semester='Spring' AND year='2010');

-- question: 14 
select course_id from teaches where (semester='Fall' AND year='2009')
AND (semester='Spring' AND year='2010');
 
-- question: 15  (pari nai)
select course_id from teaches where (semester='Fall' AND year='2009')
AND not(semester='Spring' AND year='2010');

-- question 16 
select * from instructor where salary IS NULL;
select * from instructor where salary = NULL;

-- question 17
SELECT avg(salary) AS average_salary_finance_dept
 FROM instructor where dept_name = 'Finance';

-- question: 18
select count(ID) AS TOTAL_INSTRUCTOR 
from teaches where semester='Spring' AND year='2010';

-- question: 19 (pari nai) -> But pore kmne jeno mile gelo 
select dept_name,avg(salary) from instructor where 
dept_name in (select distinct dept_name from instructor)
group by dept_name;

-- question 20
select ID from teaches where semester='Spring' AND year='2010';

select dept_name,count(ID) from instructor where ID IN (
select ID from teaches where semester='Spring' AND year='2010')
GROUP BY dept_name;

-- question 21: (pari nai)
select * from instructor;

select dept_name,average from 
(select dept_name, avg(salary) as average from 
instructor group by dept_name) AS T where T.average>42000;


-- question: 22 (pari nai)

select T.course_id ,T.sec_id,avg(S.tot_cred) from  student S
inner join 
(select * from takes where year='2009') AS  T
on S.id = T.id group by T.course_id,T.sec_id having count(T.sec_id)>1 ;

-- question: 23
-- quesetion: 24

-- question: 25
select name from instructor where name!='Mozart' AND name!='Einstein';
select name from instructor where name not in('Mozart','Einstein');

-- question: 26 (instead of 110011 we use 99052->Dale)
select * from instructor;
select name from instructor where ID=99052;
select * from teaches where ID=99052;

-- Question: 27 use(dept_name=Accounting)
SELECT * from instructor;

select ID,name from instructor where salary>
(select min(salary) from instructor where dept_name='Accounting');

-- question: 28
select max(salary) from instructor where dept_name='Biology';

select ID,name from instructor where salary>
(select max(salary) from instructor where dept_name='Biology');

-- question: 29
-- with AS alias 
select dept_name,max(T.avg_salary) from 
(select dept_name, avg(salary) AS avg_salary from instructor group by dept_name) AS T;

-- with WITH alias
With T AS(
select dept_name, avg(salary) AS avg_salary from instructor group by dept_name)
select dept_name,max(T.avg_salary) from T;
    
-- question:30
-- question: 31
select course_id from course where dept_name='Biology';

select ID AS Student_ID from takes T 
inner join 
(select course_id from course where dept_name='Biology') AS R
on T.course_id = R.course_id;

-- question: 32 (pari nai)
select course_id from section where year=2009 group by course_id having count(*)=1;

-- question: 33 
select course_id from takes where year=2009 group by course_id having count(course_id)>1;

-- question: 34
-- question: 35 (pari nai)

-- question: 36 (pari nai)
select * from department;
select * from instructor;

select D.dept_name,count(ID) from department D 
inner join instructor I
on D.dept_name = I.dept_name 
group by D.dept_Name;

-- ________________________________________________________________________
-- ________________________________________________________________________
-- __________________________ PROBLEM SET:03 ______________________________
-- ________________________________________________________________________
-- ________________________________________________________________________

-- Question: 01
SELECT title from course WHERE dept_name = 'Comp. Sci.' and credits=3;


```

