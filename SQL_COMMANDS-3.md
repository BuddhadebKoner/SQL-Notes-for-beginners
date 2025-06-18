# SQL Commands - Part 3: Advanced Queries and Operations

## Initial Setup

```sql
use mydb;
```

## SQL Joins

### Table Setup for Joins
```sql
-- Joins in SQL 
-- (INNER JOIN, OUTER JOIN, CROSS JOIN, EXCLUSIVE JOIN, SELF JOIN )
-- using customer and order table 
-- (without foreign key joins can possible, only needs common column)

create table customer (c_id int primary key,c_name varchar(20));
insert into customer values(1,"Ram"),(2,"Rahul"),(3,"Riti");
select * from customer;

create table orders (o_id int primary key, item_name varchar(20),c_id int);
insert into orders values(101,"Friuts",2),(102,"Toys",3),(103,"Grocery",4);
select * from orders;
```

### INNER JOIN
```sql
-- INNER JOIN 
-- 1. Get common elements between two tables -> C
select customer.c_id,customer.c_name, orders.o_id,orders.item_name 
from customer inner join orders on customer.c_id = orders.c_id;

-- ON or WHERE for inner join--
select customer.c_id,customer.c_name, orders.o_id,orders.item_name 
from customer inner join orders where customer.c_id = orders.c_id;
```

### OUTER JOINS

#### LEFT JOIN
```sql
-- (outer joins category - LEFT JOIN, RIGHT JOIN, FULL JOIN)

-- LEFT JOIN 
-- 1. Get common elements between two tables as well as all the elements 
-- of left table -> A + C.
-- 2. Getting null value in right table's columns in the records 
-- which found in left table but not in right table. 
select customer.c_id,customer.c_name, orders.o_id,orders.item_name 
from customer left join orders on customer.c_id = orders.c_id;
-- here customer -> left table, orders -> right table
```

#### RIGHT JOIN
```sql
-- RIGHT JOIN (
-- 1. Get common elements between two tables 
-- as well as all the elements of right table -> B + C. 
-- 2. Getting null value in left table's columns in the records 
-- which found in right table but not in left table.
select customer.c_id,customer.c_name, orders.o_id,orders.item_name 
from customer right join orders on customer.c_id = orders.c_id;

select orders.o_id,orders.item_name, customer.c_id,customer.c_name 
from customer right join orders on customer.c_id = orders.c_id;
-- here customer -> left table, orders -> right table
```

#### FULL JOIN
```sql
-- FULL JOIN 
-- 1. Get common elements between two tables 
-- as well as all the elements of left table and right table.
-- 2. Getting null value in right table's columns in the records 
-- which found in left table but not in right table and vise versa.
-- 3. The syntax for FULL JOIN is different compared to other SQL databases
-- like PostgreSQL, SQL Server.
-- 4. MySQL does not support the FULL JOIN directly, so we use a combination 
-- of LEFT JOIN, RIGHT JOIN and UNION to achieve result.
-- 5. UNION - use to get all the rows between two select operations without duplicates.
-- 6. (LEFT JOIN) UNION (RIGHT JOIN) 
--  == ( A + C )   UNION  ( B + C )
--            ==  A + B + C                 

select * from customer LEFT join orders 
on customer.c_id = orders.c_id
UNION	
select * from customer RIGHT join orders 
on customer.c_id = orders.c_id;
```

### CROSS JOIN
```sql
-- CROSS JOIN 
-- 1. Combines each row of the first table with every row of the second table.
-- 2. Getting total number of rows = m*n 
-- where m = no of rows of the 1st table and n = no of rows in the second table.
select * from customer cross join orders;
select * from customer cross join orders on customer.c_id = orders.c_id;
select * from customer cross join orders on customer.c_id != orders.c_id;
```

### EXCLUSIVE JOINS

#### LEFT EXCLUSIVE JOIN
```sql
-- (exclusive joins category - LEFT EXCLUSIVE JOIN, RIGHT EXCLUSIVE JOIN, FULL EXCLUSIVE JOIN)

-- LEFT EXCLUSIVE JOIN 
-- 1. Get the elements of Left table by excluding the common elments of the two table -> A - C.
-- 2. by adding only the un-common rows -> WHERE  RightTable.ColumnName IS NULL
select customer.c_id,customer.c_name, orders.o_id,orders.item_name 
from customer left join orders on customer.c_id = orders.c_id
WHERE orders.c_id IS NULL;
```

#### RIGHT EXCLUSIVE JOIN
```sql
-- RIGHT EXCLUSIVE JOIN 
-- 1. Get the elements of Right table by excluding the common elments of the two table -> B - C.
-- 2. by adding only the un-common rows -> WHERE LeftTable.ColumnName IS NULL
select customer.c_id,customer.c_name, orders.o_id,orders.item_name 
from customer right join orders on customer.c_id = orders.c_id
WHERE customer.c_id IS NULL;

select orders.o_id,orders.item_name, customer.c_id,customer.c_name
from customer right join orders on customer.c_id = orders.c_id
WHERE customer.c_id IS NULL;
```

#### FULL EXCLUSIVE JOIN
```sql
-- FULL EXCLUSIVE JOIN 
-- 1. Excluding the common elements between two tables 
-- and adding only the elements of left table and right table those are un-common.
-- 2. MySQL does not support the FULL JOIN directly, so we use a combination 
-- of LEFT EXLUSIVE JOIN, RIGHT EXLUSIVE JOIN and UNION to achieve result.
-- 3. UNION - use to get all the rows between two select operations without duplicates.
-- 4. (LEFT EXLUSIVE JOIN) UNION (RIGHT EXLUSIVE JOIN) 
--  == ( A - C )   UNION  ( B - C )
--          ==   A + B - C                 

select * from customer LEFT join orders 
on customer.c_id = orders.c_id WHERE orders.c_id IS NULL
UNION	
select * from customer RIGHT join orders 
on customer.c_id = orders.c_id WHERE customer.c_id IS NULL;
```

## SELF JOIN

### Example 1: Mentor-Student Relationship
```sql
-- SELF JOIN
-- 1. A self join is a type of join where a table is joined with itself
-- 2. It is a type of Inner Join 
-- 3. Cross join + condition

-- Example 1: 
create table mentor_student 
(s_id int primary key,
s_name varchar(20),
mentor_id int );

insert into mentor_student values
(1,"Ram",null), -- Ram(1) has no mentor
(2,"Rahul",1), -- Mentor name of Rahul is Ram(1) 
(3,"Riti",1),  -- Mentor name of Riti is Ram (1)
(4,"Riya",3);  -- Mentor name of Riya is Riti (3)

-- Query - provide the name of the students and their mentor
select ms2.s_name as student_name, ms1.s_name as mentor_name
from mentor_student as ms1 join mentor_student as ms2
on ms1.s_id = ms2.mentor_id;  

-- ON or WHERE --
select ms2.s_name as student_name, ms1.s_name as mentor_name
from mentor_student as ms1 join mentor_student as ms2
where ms1.s_id = ms2.mentor_id; 
```

### Example 2: Course Enrollment
```sql
-- Example 2:
create table study(s_id int, c_id int, since_year int);
insert into study values
(1,101,2016), -- student 1 enrolled in course 101
(2,102,2017), -- student 2 enrolled in course 102
(1,102,2017); -- student 1 enrolled in course 102

select * from study;
-- Query - Find the Student id who has enrolled in at least two courses.
select s2.s_id, s1.c_id 
from study as s1 join study as s2 
where s1.s_id = s2.s_id AND s1.c_id != s2.c_id; 
```

## UNION Operations

### UNION
```sql
-- UNION  using customer and order table
-- 1. Used to combine the results of two or more SELECT queries into a single result 
-- set and gives the unique rows by removing duplicate rows.
-- Things to keep in mind
-- 1. Each SELECT command within the UNION must retrieve the same no of columns else cause error.
-- 2. The data types of the columns in corresponding positions across SELECT statement 
-- should match else produce wrong output.
-- 3. Column should be listed in the same order across all select statement

select c_id from customer UNION select c_id from orders;
select c_id,c_name from customer UNION select c_id,item_name from orders;
select c_id,item_name from orders union select c_id,c_name from customer;
select * from orders union select * from customer; -- error : The used SELECT statements have a different number of columns	
select c_id,c_name from customer UNION select item_name,c_id from orders; -- wrong output
```

### UNION ALL
```sql
-- UNION ALL 
-- 1. used to combine result set of two or more SELECT queries but it
-- does not remove duplicate rows from SELECT statement result set.

select c_id from customer UNION ALL select c_id from orders;
select c_id,c_name from customer UNION ALL select c_id,item_name from orders;
```

## SubQueries

```sql
-- SubQueries in SQL using employee

-- Query 1. Find all the employees who have salary greater than the min salary.
select * from employee where salary > (select min(salary) from employee);

-- Query 2. Find all the employees with the minimum age.
select * from employee where age = (select min(age) from employee);

-- Query 3. Find the employees who is having age greater than min_age.
select * from employee where age > (select min(age) from employee);

-- Query 4. Print the employees with the average age and age of the employees 
select employee.age,(select avg(age) from employee) as AVG_age from employee;
select age,(select avg(age) from employee) as AVG_age from employee;
```

## Nth Highest Salary

```sql
-- Nth Highest Salary in a given dataset 
-- step 1. Select the column which we want to show the final result i.e salary
-- step 2. Order the distinct salary in descending order so that we have the max at the first and also removes duplicate value.
-- step 3. Now the value of n could be 1,2,3.....n, so we have to make the query in 
-- such a way so that whatever be the value of n it can provide the result.
-- step 4. So at the end of the query we will provide a LIMIT so that on the data set 
-- which we have got after ordering the salary in descending order, 
-- we can fetch the nth highest one

-- Types of LIMIT 
-- LIMIT n - Retrieve the maximun n rows returned by the query
-- LIMIT m,n 
-- m -> No of rows to be skipped from beginning
-- n -> No of rows to be fetch after skipping

-- Query - Find the nth highest salary
select distinct salary from employee order by salary desc;
-- 1st highest salary
select distinct salary from employee order by salary desc limit 0,1; 
-- 2nd highest salary
select distinct salary from employee order by salary desc limit 1,1;
-- 3rd highest salary
select distinct salary from employee order by salary desc limit 2,1;
-- 4th highest salary
select distinct salary from employee order by salary desc limit 3,1;
```

## Stored Procedures

### Procedure Without Parameters
```sql
-- Stored Procedure in SQL
-- Procedures are like as function with or without parameters 
-- we can CALL the procedure multiple times to get our output  
-- without writing the same query multiple times.

-- create procedure without parameters
-- Query - getting employee names which has maximum salary
DELIMITER /
create procedure getMaxSalName()
BEGIN
select name,salary from employee where salary = 
(select max(salary) from employee);  -- if we don't change the ending symbol it will end here
END/
DELIMITER ; -- change the end symbol again to ';'

-- calling the procedure without parameters
CALL getMaxSalname();

-- delete the procedure 
drop procedure getMaxSalName ;
```

### Procedure With Parameters
```sql
-- create procedure with parameters
-- Query - getting the Highest and lowest salary of department

Delimiter @
create procedure deptMaxMinSal(IN dept varchar(10))
BEGIN
select department,max(salary),min(salary) from employee 
group by department having department = dept;
END @
Delimiter ;

-- calling the procedure with parameters
CALL deptMaxMinSal('IT');
```

## VIEWS

```sql
-- VIEWS in SQL
-- A view is a virtual table in SQL 
-- It helps in providing a filtered view of data for security purposes.
-- It helps in data abstraction, security and simplify complex queries 

-- we want to hide the age and salary of the employee
create view empView as
select id,name,department,city from employee; 

-- we want only name and department from the view
select name,department from empView;

-- dropping the views
-- 1 . 
drop view empView;
-- 2 .
drop view if exists empview;
```

## Transaction Control Language (TCL)

```sql
-- Transaction Control Language (TCL) 
set autocommit = "on";
set autocommit ="off";

-- ROLLBACK
-- The ROLLBACK command is used to undo changes made during a transaction.
-- It reverts all the changes applied to the database since the transaction began.
-- ROLLBACK is typically used when an error occurs during the execution of a transaction,
-- ensuring that the database remains in a consistent state.
rollback;

-- COMMIT 
-- It makes all the changes applied to the database since the last COMMIT or ROLLBACK
-- command permanent.
-- Once a COMMIT is executed, the transaction is considered successful, and the changes are
-- made permanent.
commit;
```