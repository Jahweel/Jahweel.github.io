## Insurance Database Project 

### Objective

This is my first SQL project where I create a model database to help me gain a better understanding of relational database concepts, including table design, contraints, and cardinality
and the relationships they form. The reason behind modeling the database based on insurance data is to help me apply the same database concepts towards real world situation, 
as well business questions scenarios.

### Background and Research 

As stated before the idea of this project is to create my own insurance database. To start, some research was needed in order understand what type of data a insurance
company might collect on its customers. The conclusion was made to focus on the type of policies offered specically 3: 
1. Travel 
2. Valuable Items 
3. Events 



```tsql 
-- Total cost of all animals 
SELECT SUM(COST) AS "TOTAL COST"
FROM PETRESCUE; 

--Alias "SUM_OF_cost"
SELECT SUM(COST) AS SUM_OF_COST 
FROM PETRESCUE; 

-- Max quantity 
SELECT MAX(QUANTITY)
FROM PETRESCUE;

--Average 
SELECT AVG(COST)
FROM PETRESCUE; 

--Average cost of rescuing dog 
SELECT AVG(COST) AS "Average Dog Cost"
FROM PETRESCUE
WHERE ANIMAL = 'Dog'; 
```  
Next scalar and string functions perform operations on input values while string is essentially a type of scalar that specifically performs its functions on strings.

```tsql 
--Rounded cost of each rescue 
SELECT ANIMAL, ROUND(COST) AS Rounded_Cost 
FROM PETRESCUE; 

--Length of each animal name 
SELECT LENGTH(Animal)
FROM PETRESCUE;  

--Animal name in uppercase 
SELECT UPPER(ANIMAL)
FROM PETRESCUE;

--Uppercase animal name with no duplicates 
SELECT DISTINCT UPPER(ANIMAL) AS "Animals"
FROM PETRESCUE;

--display all animals that are cats in lowerccase 
SELECT *, LOWER(ANIMAL)
FROM PETRESCUE 
WHERE ANIMAL = 'Cat';
``` 
Lastly date and time make use of the specific data types that are within the database. For example, the database used for this example was formatted in MMDDYYYY

```tsql
--Day of the month whn cats where rescued 
SELECT DAY(RESCUEDATE) AS RESCUE_DAY
FROM PETRESCUE 
WHERE ANIMAL = 'Cat';
-- Animals rescued on the 5thmonth 
SELECT *
FROM PETRESCUE 
WHERE MONTH(RESCUEDATE) = '5'; 

--animal rescuded on the 14th day of the month 
SELECT * 
FROM PETRESCUE 
WHERE DAY(RESCUEDATE) = '14'; 

-- display third dat of each rescue 
SELECT (RESCUEDATE + 3 DAYS)
FROM PETRESCUEl; 

---- lenght of time at shelter 
SELECT *, (CURRENT_DATE - RESCUEDATE) AS "Time At Shelter"
FROM PETRESCUE;
```
These are just basic summaries of each functions with the examples used as a source of reference if at any point I need a refresher in the future.
