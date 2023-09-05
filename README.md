# AdventureWorks2022

/*
First, connect to the Database that you want to work with
*/

select *
from HumanResources.Department;

select DepartmentID, Name, GroupName
from HumanResources.Department;

-- using Alias
select DepartmentID, Name, GroupName AS Department
from HumanResources.Department;

/*
Removing Duplicates - output/your analysis will be wrong if data has duplicates. 
The Syntax to use to avoid duplicates is DISTINCT
*/

select *
from Production.Product;

select distinct Color
from Production.Product;

--- try where null is not a color

select distinct Color
from Production.Product

-- ORDER BY Claue

select *
from Sales.SalesOrderDetail
order by OrderQty;

select OrderQty, UnitPrice, ModifiedDate
from Sales.SalesOrderDetail
order by OrderQty desc;

-- Limiting Query Results...when you have millions of rows, you may need to view only few rows to understand the table
-- this below will help you get quick understanding of the table

select top 10 *
from Production.Product;

select top 10 Name as ProductName, ListPrice

/* PREDICATES 
-- they are operators that works with the WHERE Syntax. They include:
= <>, IN, BETWEEN, LIKE(determines whether a specific character string matches a specified pattern)
AND (used when both expressions are true and often work together with BETWEEN) */

--BETWEEN
select top 3 *
from Production.TransactionHistory

select Quantity
from Production.TransactionHistory
where Quantity Between 100 and 500;

select TransactionID, Quantity, ProductID
from Production.TransactionHistory
     Where Quantity not between 100 and 500
	 And ProductID IN (784, 794);
-- Code above using IN will only query ProductID 784 and 794

-- NULL is used to record missing or unknown data value

select distinct Color
from Production.Product;

-- JOINS
-- create two tables to be able to practice joins

create table HumanResources.candidates(
  id int primary key identity,
  fullname varchar(100)not null
);

create table HumanResources.employees(
  id int primary key identity,
  fullname varchar (100) not null
  );

 -- Insert some rows into the new tables

 Insert into HumanResources.candidates(fullname)
 values
   ('John Does'),
   ('Lily Bush'),
   ('Peter Drucker'),
   ('Jane Doe');

 Insert into HumanResources.employees(fullname)
 values
   ('John Doe'),
   ('Jane Doe'),
   ('Michael Scott'),
   ('Jack Sparrow');

  select * from HumanResources.employees;
  select * from HumanResources.candidates;

  -- Let's make Candidates table the left table and Employees table the right table

  select * from HumanResources.candidates
  update HumanResources.candidates
  set fullname = 'John Doe'
  where id = 1

-- did above code because I had to update the candidates table

  select * from HumanResources.employees;

  --- you can rename candidates table as c and employees table as e just to type faster

select
  c.id CandidatesID,
  c.fullname CandidatesName,
  e.id EmployeesID,
  e.fullname EmployeeName
from
  HumanResources.candidates c
  inner join
  HumanResources.employees e
on c.fullname = e.fullname;

-- trying LEFT JOIN

select
  c.id CandidatesID,
  c.fullname CandidatesName,
  e.id EmployeesID,
  e.fullname EmployeeName
from
  HumanResources.candidates c
  left join
  HumanResources.employees e
on c.fullname = e.fullname;

-- Trying JOINS using the HumanResources Employee and Person.Person tables

select top 10 *
from HumanResources.Employee;

select top 10 *
from Person.Person;

-- Case study example: produce FirstName, LastName, BusinesssEntityID, NationalIDNumber, JobTitle

select
  PP.FirstName,
  PP.LastName,
  HRE.BusinessEntityID,
  HRE.NationalIDNumber,
  HRE.JobTitle
from HumanResources.Employee HRE
  join Person.Person PP
  on HRE.BusinessEntityID = PP.BusinessEntityID
 order by HRE.BusinessEntityID;

  /* Case Study 2:
  A person with the Business Entity ID 2510 just misplaced a personal ID, can you get the phone number, first name,
  last name so that the person can be contacted. */

  select top 10 *
from Person.Person;

  select top 10 *
  from Person.PersonPhone;

  select
  PP.FirstName,
  PP.LastName,
  PP.BusinessEntityID,
  PPH.PhoneNumber
from Person.Person PP
  join Person.PersonPhone PPH
  on PP.BusinessEntityID = PPH.BusinessEntityID
  where PP.BusinessEntityID = 2510;

  -- UNIONS Queries processes contents of two or more tables into a single table
  -- lets work with tables sales.countryregion currency and person.countryregion.
  -- Union will give you distinct values, but UNION ALL will not remove duplicate rows.
  --to use UNION, the tables must have (a) same number of column (b)data types in the columns must be similar

select *
from sales.CountryRegionCurrency
  union
select *
from Person.CountryRegion;

-- INTERSECTS... it will not return all rows in an inner join for example, it will return distinct rows in them
-- The number of columns of both tables must be the same

-- EXCEPT.. The SQL Server compares result of two sets of queries and returns the distnct rows of the first query.

-- some practice: (a) produce BusinessEntityID, NationalIDNumber, Gender and JobTitle of every employee
-- (b) produce list of female employees, include their full names.

select
BusinessEntityID,
NationalIDNumber,
Gender,
JobTitle 
from HumanResources.Employee
order by BusinessEntityID;
-- (b)
select
HRE.BusinessEntityID,
HRE.NationalIDNumber,
PP.Firstname,
PP.Lastname,
HRE.JobTitle,
HRE.Gender
from HumanResources.Employee HRE
join
Person.Person PP
on HRE.BusinessEntityID = PP.BusinessEntityID
where Gender = 'F'
order by BusinessEntityID;

/* BUILT-IN FUNCTIONS
There are: Scalar Functions, Aggregate Functions and Logical Functions.
Aggregate Functions include: Count, Sum, Average, Max, Min, Dev, Stdev*/
-- USING AGGREGATE FUNCTIONS

select * from Production.Product;
-- Example: What is the number of products (TotalProducts) in our store
-- NOTE: ALWAYS CONSIDER DISTINCT in mind to avoid repetition.

select COUNT (distinct ProductID) as TotalProducts
from Production.Product;

-- Example: find the average cost of products
select Avg(StandardCost) as AvgCost
from Production.Product;

-- which is the most expensive product?
select Max(ListPrice) as MostExpensiveProduct
from Production.Product;
-- OR
select Name, Max(ListPrice) as MostExpensiveProduct
from Production.Product
where ListPrice <> 0
Group by Name;

-- GROUP BY: It creates groups for output rows based on the values specified in the Group By Clause.
--whenever you include an aggregate function in your code, add Group By Clause to your code.

select *
from sales.SalesOrderHeader;

select CustomerID, count (*) as "Orders"
from sales.SalesOrderHeader
group by CustomerID;
-- This code above shows the count of the orders made by each customer.

-- HAVING CLAUSE: it is like the WHERE Clause, it shows a search condition to process but the difference is that it comes
-- after GROUP BY. WHERE CLAUSE filters rows while HAVING CLUASE filters the Groups based on the specified condition
--- and Groups are column based


/* DATABASE DEFINITION LANGUAGE (DDL)- SQL commands used to defined the database schema. To create, modify the structure
of objects in the database... CREATE, DROP & ALTER */
-- When creating a DB, the name of your Database should not be more than 128 characters.

create database SampleDb  

exec sp_databases -- used to view all that Databases you have

Create schema QuantumAnalytics

create table May_Cohort(
   Student_ID int,
   First_Name varchar (50) Not Null,
   Last_Name varchar (50) Not Null,
   Email varchar (100) Not Null,
   Phone varchar (20) Not Null
);

select * from May_Cohort;

insert into May_Cohort values
(00001, 'Magaret', 'Abigail', 'm.a@gmail.com', '+997654321');

insert into May_Cohort values
(00002, 'Peter', 'Tobi', 'pt@ymail.com', '000099999776'),
(00003, 'Andrew', 'Jake', 'aj@gmail.com', '000099991234'),
(00004, 'Nneka', 'Chukwu', 'achukwu@ymail.com', '00009965432');
