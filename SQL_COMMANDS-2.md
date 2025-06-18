# SQL Commands - Part 2: Data Manipulation and Queries

## Table Setup

```sql
use mydb;
CREATE TABLE marks(
std_id int primary key auto_increment,
std_name varchar(50),
marks_obtained int check (marks_obtained<=400),
total_marks int,
percentage float,
remarks varchar(4)
);

insert into marks(std_name,marks_obtained) values
("jhon",285),
("smith",248),
("emma",329),
("sandy",249),
("olivia",150),
("james",187),
("eva",352),
("noah",279),
("charlotte",282),
("elijah",301);
```

## Table Modification

### Renaming Table
```sql
-- renaming Table name
alter table marks
rename to student_marks;
-- or
rename table marks to student_marks;
```

### Column Operations
```sql
-- RENAME COLUMN use to change the name of the column only but it can't change the data type
alter table student_marks rename column total_marks to full_marks;
-- then we can modify the data type of it using MODIFY command
alter table student_marks modify full_marks varchar(10);

-- above two step can be done using CHANGE command
alter table student_marks change full_marks Overall_marks int;
```

## Basic DML Operations

### UPDATE, DELETE, SELECT with WHERE Clause
```sql
-- update record (where clause)
update student_marks set std_name = "eliza" where std_id = 10; 

-- delete record (where clause)
delete from student_marks where std_id = 9; 

-- select record (where clause)
select * from student_marks where marks_obtained>=250; 
select * from student_marks where marks_obtained<250;

select std_id,std_name from student_marks;
select * from student_marks;
```

### Table Cleanup
```sql
truncate table student_marks;  -- delete the rows(records) keeping the structure(schema) as empty
drop table student_marks; -- delete the rows(records) as well as the complete structure(Schema) 
                          -- i.e., delete the whole table from the database
```

## Employee Table Setup

```sql
create table employee (
id int primary key,
name varchar(50),
age tinyint check(age>=18),
department varchar(10),
city varchar(20),
salary int);

insert into employee values
(1,"Rahul",25,"IT","Mumbai",15000),
(2,"Ashraf",26,"HR","Pune",20000),
(3,"Abhimanyu",25,"IT","Mumbai",25000),
(4,"Aditya",25,"Marketing","Pune",24000),
(5,"Raj",24,"Finance","Indore",15000);

select * from employee;
```

## DISTINCT Command

```sql
-- DISTINCT command 
-- used within the SELECT statement to retrieve unique
-- values from a column or combination of columns.

select distinct department from employee;
select distinct city from employee;
select distinct age,department from employee;
```

## Arithmetic Operators

```sql
-- ARITHMETIC OPERATORS(+,-,/,*,%(REMAINDER)) using student_marks table
update student_marks set overall_marks=100+100; 

update student_marks set overall_marks = overall_marks*4 - 400;

alter table student_marks modify column percentage tinyint;

update student_marks set percentage =marks_obtained / overall_marks * 100;

select * from student_marks where (percentage % 2 =0); -- even percentage
select * from student_marks where (percentage % 2 =1); -- odd percentage

select * from student_marks;
```

## Logical Operators

```sql
-- LOGICAL OPERATORS (AND,OR,NOT) , IN OPERATOR using employee table
select * from employee where age = 25 AND city = "mumbai";
select * from employee where age = 25 OR city = "mumbai";
select * from employee where city IN ("Mumbai","Pune");
select * from employee where city NOT IN ("Mumbai","Pune");

-- IS NULL,IS NOT NULL OPERATOR using employee table
select * from employee where department IS NULL; 
select * from employee where department IS NOT NULL; 
```

## Bitwise Operators

```sql
-- BITWISE OPERATOR (&->AND,|->OR)
-- converting the integer value into bits (0,1) then we can either use & , | operator
create table bitwise(
number_1 int,
number_2 int,
AND_op int,
OR_op int
);
insert into bitwise(number_1,number_2)
values (10,12),(13,15);

update bitwise set AND_op = number_1 & number_2;
update bitwise set OR_op = number_1 | number_2; 

-- Illustration 
-- 10  = 1010                 10  = 1010
-- 12  = 1100                 12  = 1100
-- ---------------------------------------------
-- AND = 1000 = 8             OR  = 1110 = 14

-- 13  = 1101                 13  = 1101
-- 15  = 1111                 15  = 1111
-- ---------------------------------------------
-- AND = 1101 = 13            OR  = 1111 = 15

select * from bitwise;
drop table bitwise;
```

## Pattern Matching and Range Operators

```sql
-- LIKE & WILDCARD OPERATOR (%,_) using employee table
select * from employee;
select * from employee where name LIKE "A%"; -- name starts with 'A', % denotes any character
select * from employee where name LIKE "_A%"; -- name can start with any character but the  
                                              -- 2nd character must be A and rest any character

-- BETWEEN OPERATOR
select * from employee where salary between 20000 AND 25000;
select * from employee where salary between 10000 AND 20000;
```

## SQL Clauses

### WHERE Clause
```sql
-- WHERE clause
select * from employee WHERE age>=25 AND city="Mumbai";
```

### LIMIT Clause
```sql
-- LIMIT clause use to get only specified number or records
select * from employee LIMIT 3; -- only shown 3 records 
```

### ORDER BY Clause
```sql
-- ORDER BY clause (ASC -> Ascending / DESC -> Descending)
select * from employee order by salary; -- by default ASC 
select * from employee order by salary ASC; 
select * from employee order by salary DESC; 
select * from employee where salary >=20000 order by salary asc; 
select * from employee where salary >=20000 order by salary desc; 

select * from employee order by salary desc limit 2; -- getting highest 2 member salary
-- or --
select distinct salary, id, name,age,department,city from employee order by salary desc limit 2; 
```

### GROUP BY Clause
```sql
-- GROUP BY clause with
-- Aggregate function (COUNT(),SUM(),AVG(),MIN(),MAX(),GROUP_CONCAT()) discuss later
select department,AVG(salary) as AVGsal from employee group by department; -- AVGsal is not a physical column
select department,MAX(salary) as MAXsal from employee group by department;
select department,MIN(salary) as Minsal from employee group by department;
select department,count(salary) as count from employee group by department;

select department,AVG(salary) as AVGsal, MAX(salary) as MAXsal, MIN(salary) as Minsal, count(name) as count from employee group by department;
```

### HAVING Clause
```sql
-- HAVING clause with
-- Aggregate function (COUNT(),SUM(),AVG(),MIN(),MAX(),GROUP_CONCAT()) discuss later
select department,AVG(salary)  from employee group by department having AVG(salary)>=20000;
select department,MAX(salary) as MAXsal from employee group by department having MAXsal>=20000;
select department,MIN(salary) as Minsal from employee group by department having MINsal<20000;
select department,count(salary) as count from employee group by department having count>1;

select department,
AVG(salary) as AVGsal, 
MAX(salary) as MAXsal, 
MIN(salary) as Minsal,
count(name) as no_of_employee 
from employee group by department having MAXsal>=20000;
```

## Aggregate Functions

```sql
-- Aggregate function (COUNT(),SUM(),AVG(),MIN(),MAX(),GROUP_CONCAT())

select count(id) as no_of_employee from employee; 
select sum(salary) from employee;
select avg(Salary) from employee;
select min(salary) from employee;
select max(salary) from employee;
select group_concat(name) from employee;
select group_concat(department) from employee;
select group_concat(city) from employee;
select group_concat(salary) from employee;
```

## Practice Questions

```sql
-- Practice Questions
-- 1. Write a query to find the total number of employees in each city
select city,count(name) as number_of_employees from employee group by city;

-- 2. Write a Query to find the maximum salary of employees 
-- in each city in descending order.
select city,max(salary) from employee group by city order by max(salary) desc;

-- 3. Write a query to display the department names alongside 
-- the total count of employees in each department, sorting the results by the total
-- number of employees in descending order.
select department, count(name) as total_count from employee 
group by department 
order by total_count desc;

-- 4. Write a query to list the departments where the average salary is greater 
-- than 15000, also display the department name and the average salary.	
select department , avg(salary) from employee 
group by department having avg(salary)>15000;
```

## SQL Command Execution Order

| Sno | Command  | Use Case                                    |
|-----|----------|---------------------------------------------|
| 1.  | SELECT   | Retrieve from the database                  |
| 2.  | FROM     | Identify the table                          |
| 3.  | WHERE    | Filter rows based on some conditions       |
| 4.  | GROUP BY | Group rows that have the same values       |
| 5.  | HAVING   | Filter groups based on some conditions     |
| 6.  | ORDER BY | Sort the result set either asc/desc        |
| 7.  | LIMIT    | Limit the number of rows returned          |