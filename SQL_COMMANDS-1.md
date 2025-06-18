# SQL Commands - Part 1: Data Definition Language (DDL)

## Database Creation

```sql
-- Data Definition Language (DDL)
CREATE DATABASE mydb;
-- or --
CREATE DATABASE IF NOT EXISTS mydb;
use mydb; 
```

## NOT NULL Constraints

### During Table Creation
```sql
create table student1
(id int not null);
-- or --  
create table if not exists student1
(id int not null);
drop table student1;
-- or --
drop table if exists student1;
```

### After Table Creation
```sql
create table student1
(id int);
-- NOT NULL constraints after table creation 
alter table student1
modify column id int not null;

drop table student1;
```

## UNIQUE Constraints

### During Table Creation
```sql
create table student1
(id int unique);
drop table student1;
```

### After Table Creation
```sql
create table student1
(id int);
-- UNIQUE constraints after table creation process-1
alter table student1
modify column id int unique;

-- UNIQUE constraints after table creation process-2
alter table student1
add constraint unique(id);
-- or --
alter table student1
add constraint unique(id);

drop table student1;
```

## PRIMARY KEY Constraints

### During Table Creation
```sql
create table student1
(id int primary key);
drop table student1;
```

### After Table Creation
```sql
create table student1
(id int);
-- PRIMARY KEY constraints after table creation process-1
alter table student1
modify column id int primary key;

-- PRIMARY KEY constraints after table creation process-2
alter table student1
add constraint primary key(id);
drop table student1;
```

## DEFAULT Constraints

### During Table Creation
```sql
create table student1
(college_name varchar(50) default "ABC");
drop table student1;
```

### After Table Creation
```sql
create table student1
(college_name varchar(50));
-- DEFAULT constraints after table creation process-1
alter table student1
modify column college_name varchar(50) default "ABC";
-- test 
insert into student1
values();
select * from student1;

-- DEFAULT constraints after table creation process-2
alter table student1
alter column college_name set default "ABC";
-- test 
insert into student1
values();
select * from student1;
drop table student1;
```

## CHECK Constraints

### During Table Creation
```sql
create table student1
(age tinyint check(age>=18));
-- test 
insert into student1 -- check is violated
values(17);
insert into student1  -- accepted
values(20);
select * from student1;
drop table student1;
```

### After Table Creation
```sql
create table student1
(age tinyint);
-- CHECK constraints after table creation process-1
alter table student1
modify column age tinyint check(age>=18);  -- constraint name given by compiler let(student1_chk)

-- test 
insert into student1 -- student1_chk is violated
values(17);
insert into student1  -- accepted
values(20);
select * from student1;

-- CHECK constraints after table creation process-2
alter table student1
add constraint chk_age CHECK (age>=18) ;  -- constraint name given by programmer (chk_age)

-- test 
insert into student1 -- chk_age is violated
values(17);
insert into student1  -- accepted
values(20);
select * from student1;

-- removing the CHECK constraint
alter table student1
drop constraint chk_age;  

drop table student1;
```

## FOREIGN KEY Constraints

### Parent Table Setup
```sql
create table courses -- Parent/Base/Referenced Table (course_id is being referenced)
(
id int primary key,
course_name varchar(50),teacher_name varchar(50)
);
rename table courses to course;

-- renaming the column name 
alter table course
rename column id to course_id;

-- delete existing column
alter table course
drop column teacher_name;

-- adding new column
alter table course
add column teacher_name varchar(50);
```

### Child Table with Foreign Key
```sql
-- FOREIGN KEY constraint during table creation
create table teacher   -- Child/Referencing Table
(
id int primary key,
teacher_name varchar(50),
unique(teacher_name),
phone_no bigint not null unique check(length(phone_no)=10),
course_id int,
foreign key (course_id) references course(course_id)   -- constraint name given by compiler let(teacher_fk)
on delete set null  
on update cascade,
institution_name varchar(50) default "SANAKA"
); 

-- FOREIGN KEY constraint after table creation process-1
ALTER table teacher
add constraint fktc foreign key (course_id) references course(course_id)
on delete set null  
on update cascade ;   -- constraint name given by programmer (fktc) 

-- removing FOREIGN KEY constraint
ALTER table teacher 
drop constraint fktc;

-- removing PRIMARY KEY constraint
ALTER table teacher 
drop primary key;  

-- removing CHECK constraint
ALTER table teacher 
drop constraint teacher_chk_1; 
```

### Column Position Management
```sql
-- changing position of the column after any column
alter table teacher 
modify institution_name varchar(50) after phone_no; 

-- changing position of the column as first
alter table teacher 
modify institution_name varchar(50) first; 
alter table teacher 
rename column institution_name to college_name;
alter table teacher 
modify college_name varchar(100);
```

## DELETE and UPDATE Operations Between Parent and Child Tables

### DELETE Options:
- **on delete no action** - Default case where query shows error and no deletion performed
- **on delete restrict** - same as on delete no action
- **on delete cascade** - (not recommended) deletion of record from any table cause deletion of record in other table
- **on delete set null** - deletion of record from any table put null value in the record in other table

### UPDATE Options:
- **on update no action** - Default case where query shows error and no updation performed 
- **on update restrict** - same as on update no action
- **on update cascade** - updation of record from any table cause updation of record in other table
- **on update set null** - updation of record from any table put null value in the record in other table

## AUTO_INCREMENT

```sql
use college;

-- auto_increment used to generate a unique identifier for new rows in a table. 
-- It automatically increments the value of the column for each new row. 
-- This is commonly used for primary keys By default, the AUTO_INCREMENT value starts at 1.
-- Note: The AUTO_INCREMENT attribute can only be used with integer data types such as TINYINT, 
-- SMALLINT, MEDIUMINT, INT, or BIGINT.

-- Parent Table
create table student(
id int primary key auto_increment, -- column shows in schema (AI PK) AUTO_INCREMENT PRIMARY KEY
student_name varchar(50)
);

-- Child Table
create table course(
course_id int primary key auto_increment,  
course_name varchar(20),
student_id int);

-- starting auto_increment from 100
alter table course 
auto_increment = 100;  

-- inserting data in both tables
insert into student(student_name)
values
("Ram"),
("Laxman");

insert into course(course_name,student_id)
values
("sql",1),
("java",2);
```

## Foreign Key Relationship Examples

### ON DELETE RESTRICT and ON UPDATE NO ACTION
```sql
-- on DELETE no action/restrict and on UPDATE no action/restrict (only PRIMARY KEY of Parent and FOREIGN KEY of Child)
alter table course
add constraint fk FOREIGN KEY(student_id) references student (id)
on DELETE restrict 
on UPDATE no action; 
-- restrict and no action both are same (as these are default case not mandatory to declare explicitly)

-- DELETE, UPDATE and INSERT - Parent Table 
delete from student where id=1;  -- error: cannot Delete or update parent row
update student set id=3 where id=1; -- error: cannot Delete or update parent row
insert into student (id) values(4); -- successful addition in Parent Table

-- DELETE, UPDATE and INSERT - Child Table
delete from course where student_id=1;  -- successful deletion, now we can delete the related record from Parent Table
update course set student_id=3 where student_id=1; -- error: cannot add or update child row
insert into course (student_id) values(6);  -- error: cannot add or update child row : a foreign key constraint fails
insert into course (course_name) values("python");  -- successful addition but foreign key = null.
```

### ON DELETE CASCADE and ON UPDATE CASCADE
```sql
alter table course
add constraint fk FOREIGN KEY(student_id) references student (id)
on DELETE cascade 
on UPDATE cascade; 

-- DELETE, UPDATE and INSERT - Parent Table 
delete from student where id=1;  -- successful deletion of the record from both the Tables.
update student set id=3 where id=1; -- successful updation of the record from both the Tables.
insert into student (id) values(4); -- successful addition in Parent Table

-- DELETE, UPDATE and INSERT - Child Table 
delete from course where student_id=1;  -- successful deletion only from child Table.
update course set student_id=3 where student_id=1; -- error: cannot add or update Foreign Key. 
insert into course (student_id) values(6);  -- error: cannot add or update child row : a foreign key constraint fails
insert into course (course_name) values("python");  -- successful addition but foreign key = null.
```

### ON DELETE SET NULL and ON UPDATE SET NULL
```sql
alter table course
add constraint fk FOREIGN KEY(student_id) references student (id)
on DELETE set null 
on UPDATE set null; 

-- DELETE, UPDATE and INSERT - Parent Table
delete from student where id=1;  -- successful deletion of the record from Parent Table and put 
                                -- null value in the Foreign key of Child Table
update student set id=3 where id=1; -- successful updation of the record from Parent Table
                                    -- and put null value in the Foreign key of Child Table
insert into student (id) values(4); -- successful addition in Parent Table

-- DELETE, UPDATE and INSERT - Child Table 
delete from course where student_id=1;  -- successful deletion only from child Table.
update course set student_id=3 where student_id=1; -- error: cannot add or update Foreign Key.
insert into course (student_id) values(6);  -- error: cannot add or update child row : a foreign key constraint fails 
insert into course (course_name) values("python");  -- successful addition but foreign key = null.
```

### Best Practice: ON DELETE SET NULL and ON UPDATE CASCADE
```sql
-- best case is on DELETE set null and on UPDATE cascade (only PRIMARY KEY of Parent and FOREIGN KEY of Child)
alter table course
add constraint fk FOREIGN KEY(student_id) references student (id)
on DELETE set null 
on UPDATE cascade; 

-- DELETE, UPDATE and INSERT - Parent Table 
delete from student where id=1;  -- successful deletion of the record from Parent Table and put 
                                -- null value in the Foreign key of Child Table
update student set id=3 where id=1; -- successful updation of the record from both Tables.
insert into student (id) values(4); -- successful addition in Parent Table

-- DELETE, UPDATE and INSERT - Child Table 
delete from course where student_id=1;  -- successful deletion only from child Table.
update course set student_id=3 where student_id=1; -- error: cannot add or update Foreign Key. 
insert into course (student_id) values(6);  -- error: cannot add or update child row : a foreign key constraint fails
insert into course (course_name) values("python");  -- successful addition but foreign key = null.
```

## Table Deletion Rules
```sql
drop table course; --  Child Table must drop first else cause error 
drop table student; -- Parent Table must drop after dropping of Child Table

truncate table course; -- child Table can be truncate if foreign key exists or not
truncate table student; -- parent Table can not be truncate if foreign key exists 

alter table course
drop constraint fk;

select * from student;
select * from course;
```