## Insurance Database Project 

>Note: this project was originally done within Oracle Developer but has been transitioned to PostgreSQL. Some Queries and diagrams may be specific to one tool rather than the other and will be noted as such. 

### Objective

This is my first SQL project where I create a model database to help me gain a better understanding of relational database concepts, including table design, constraints, cardinality, and the relationships they form. The reason behind modeling the database based on insurance data is to help me apply the same database concepts towards the real-world situation, 
as well as business questions scenarios. 

### Background and Research 

As stated before the idea of this project is to create my insurance database. To start, some research was needed to understand what type of data an insurance
a company might collect on its customers. The conclusion was made to focus on the type of policies offered specifically 3: 
1. Travel 
2. Valuable Items 
3. Events 

These are real-world policies that are offered by insurance companies. Data such as policy start and end dates, deductibles, and more would help create the foundation for each policy moving forward. The most important item to remember however is the customer. As I go through the project 
I must keep in mind that the focus of the data and its origins would be from the customer and that is where the database would center on. For reference please see the image below.

![This is an image](https://i.imgur.com/xngn0W3.png)
>Note: This is a final ERD model from Oracle Developer and serves as a starting point to understanding each table relationship within the database. I will go over the creation of each table in further detail below. 

## Data 

Data used in the project can be found in each respective tables folder. They are marked in csv files.
[Data Folders](https://github.com/Jahweel/Insurance_Database)

### Table Creation 
 1. Customer Table 

The first table to be created would be the customer's table. This table would be the center of the database as in a real-world scenario customers/policyholders would be the center of a companies focus. Before I begin it's a good idea to go over certain terms and definitions that will come up repeatedly during each table creation 

- **Primary Key**: A Unique identifier that cannot be repeated or used again 
- **Foreign Key**: A key that is a primary key within another table and links two tables 
- **Not Null**: No null data inputs allowed 
- **Integer**: Numerical data input only 
- **Date**: Dates and time 
- **Varchar**: Allows varying characters and special symbols 


To begin the process this SQL script was run: 
```tsql 
 CREATE TABLE customers 
(customer_id INTEGER 
	CONSTRAINT cust_customer_id_pk PRIMARY KEY 
 ,f_name VARCHAR NOT NULL
 ,l_name VARCHAR NOT NULL
 ,street_address VARCHAR(100) 
 ,zip_code INTEGER
 ,state_province VARCHAR (2) 
 ,dob DATE NOT NULL 
 ,phone VARCHAR (12) NOT NULL UNIQUE  
 ,email VARCHAR (50) NOT NULL UNIQUE 
 ,gender VARCHAR (1)
 ); 
```  
> This Is the Customer table. Customer_id is set as the Primary Key and data types were determined based on the type of data that each column would house with respective limits as well. 

Within the customer table, the following information would be housed whenever a customer purchases an insurance policy 
  - Customer ID is a unique numerical ID given to each customer 
  - First and last name 
  - Address, state, and zip code 
  - Date of birth 
  - phone number 
  - email 
  - Gender  
>While doing research the information above is typically information insurance quotes prompt for when generating quotes. 

Each column name is based on the type of information that the column would have. For example, the **f_name** column contains customers **first name**, This naming convention will be applied to all tables.


  #### 2. Events, Valuable Items, Travel Tables  

The following tables are the specific tables for each respective insurance type:

```tsql 
-- Events table 
CREATE TABLE events
(event_id INTEGER PRIMARY KEY 
 ,e_type VARCHAR 
 ,e_date DATE NOT NULL 
 ,number_attendees INTEGER NOT NULL
);  

-- Travel table 
CREATE TABLE travel 
(travel_id INTEGER PRIMARY KEY
 ,t_state VARCHAR (2) NOT NULL
 ,country VARCHAR (2) NOT NULL 
 ,destination VARCHAR(50) NOT NULL 
 ,arrival_date DATE NOT NULL
 ,departure_date DATE NOT NULL
 ,book_date DATE 
 ,trip_cost INTEGER NOT NULL CHECK(trip_cost>0)
); 

-- Valuable items 
CREATE TABLE valuable_items 
(item_id INTEGER PRIMARY KEY 
 ,item_value INTEGER NOT NULL
 ,item_type VARCHAR(50) NOT NULL 
 ,repair_cost INTEGER 
 ,replacment_cost INTEGER  
); 
 
--changing table name to be more consistent 
ALTER TABLE valuable_items 
RENAME TO items; 
```
> Note: Check is used to make sure that the numerical value within the trip cost is always greater than zero. Also, alter table is used to update table names without having to delete the table and start over. 

  #### 3. Insurance Tables  
The next Group of tables to be created were the insurance tables for each respective insurance type (*Valuable items, event, travel*) The columns and rows included are within the tables in each are nearly identical as seen in the script below:


```tsql 
--Travel insurance table 
CREATE TABLE travel_insurance 
(insurance_id INTEGER PRIMARY KEY 
 ,t_type VARCHAR(25)
 ,t_coverage integer CHECK(t_coverage>0)
 ,deductibles INTEGER NOT NULL
);  

--Item insurance table 
CREATE TABLE item_insurance 
(item_insurance_id INTEGER PRIMARY KEY 
 ,i_type VARCHAR(50)
 ,i_coverage INTEGER CHECK(i_coverage>0)
 ,i_deductibles INTEGER NOT NULL 
); 

--Event insurance data 
CREATE TABLE event_insurance 
(event_insurance_id INTEGER PRIMARY KEY 
 ,e_insurance_type VARCHAR 
 ,e_coverage INTEGER NOT NULL CHECK(e_coverage>0)
 ,e_deductibles INTEGER NOT NULL
); 

--changing the primary key to be shorter and consistent with other column names 
ALTER TABLE event_insurance
RENAME event_insurance_id TO e_insurance_id;
``` 
>Note: it is important to see that while each table columns and rows are identical however they should have different and consistent names. This will help in the future when performing joins and be less confusing. 

  #### 4. Policy Tables 
The last set of tables created is the policy tables. These Tables are what connect the customer table with all other tables for each specific insurance type.
I will come back to elaborate on the relationships formed by the table later on in the blog post. 
The table scrips follow below: 

```tsql 
--Event table 
CREATE TABLE event_policy 
(event_policy_number INTEGER PRIMARY KEY 
 ,e_coverage_start_date DATE NOT NULL 
 ,e_coverage_end_date DATE NOT NULL 
 ,event_state VARCHAR(2)
 ,e_insurance_id INTEGER 
 	REFERENCES event_insurance(e_insurance_id)
 ,event_id INTEGER 
 	REFERENCES events(event_id)
 ,customer_id INTEGER 
 	REFERENCES customers(customer_id )
);

--Item policy 
CREATE TABLE item_policy 
(item_policy_number INTEGER PRIMARY KEY 
 ,i_coverage_start_date DATE NOT NULL
 ,i_coverage_end_date DATE NOT NULL
 ,i_policy_dependents INTEGER 
 ,item_insurance_id INTEGER 
 	REFERENCES item_insurance(item_insurance_id)
 ,item_id INTEGER
 	REFERENCES valuable_items(item_id)
 ,customer_id INTEGER 
 	REFERENCES customers(customer_id)
);  

--Travel policy 
CREATE TABLE travel_policy
(policy_number INTEGER PRIMARY KEY 
 ,customer_id INTEGER 
 	REFERENCES customers(customer_id) 
 ,insurance_id INTEGER
 	REFERENCES travel_insurance(insurance_id)
 ,travel_id INTEGER
 	REFERENCES travel(travel_id)
 ,t_coverage_start_date DATE NOT NULL 
 ,t_coverage_end_date DATE NOT NULL
 ,policy_dependents INTEGER 
);  

--changing the primary key to be more specific 
ALTER TABLE travel_policy 
RENAME policy_number TO travel_policy_number;
```
### **Table Relationships**

Understanding the relationships between tables will help generate better queries. To explain this I will focus on the customer and travel tables (*The same principle will apply to customers and other tables*) below: 

![This is a image](https://i.imgur.com/cldMn5A.png) 
>Note This is an ERD diagram from PostgreSQL 

The ERD shown in the image above shows a ternary relationship between the 3 tables. Based on the diagram it can be read like this: **A customer purchases insurance for a planned travel date.** The customer, travel, and travel insurance tables are all independent and do not have any foreign keys. However, the relationship that they form creates an additional associative table known as the travel policy. This table features foreign keys from the other three tables, creating its primary key. Now with the addition of this new table the diagram and be read like this: **A customer purchases an insurance policy with travel insurance for a travel date.**

## **Queries** 
Now here comes the fun part, queries. I went through many queries from simple ones just to see if I could pull information from a table to my finalize queries that are shown below. The main objective is to create queries that might help answer certain business questions or at least provide value to the business. **It is extremely important to not lose sight of the business objective** 
>:warning: Certain queries below will only run on Oracle due to features being specific to it, and will be noted 

  ### **Query 1** 

```tsql 
--Finding the Avererage deductible cost for each insurance type
SELECT AVG(e.e_deductibles) AS "Average Event Deductibles", AVG(t.t_deductibles) AS "Average Travel Deductible",
AVG(i.i_dedcutibles) AS "Average Item Deductibles" 
FROM travel_insurance t 
CROSS JOIN event_insurance e 
CROSS JOIN item_insurance i; 
```
> This query uses a cross join as the three columns in the select statement are all independent of each other and do not have a foreign/primary key to reference to 

  **Output** 
  
  ![This is a image](https://imgur.com/AlvlhTJ.png) 
  >The output of the queries presents us with the average cost of a deductible based on insurance type. This would be beneficial to know where customers would spend the most between events, travel, and valuable items.  

   **Query 2** 
  
```tsql 
-- Looking for trends between event insurance and attendees 
SELECT i.e_insurance_type AS "Insurance", e.e_type AS "Event", e.number_attendees AS "Attendees"
FROM event_insurance i JOIN event_policy p 
ON (i.e_insurance_id=p.e_insurance_id) 
JOIN events e 
ON (e.event_id=p.event_id) 
ORDER BY e.number_attendees DESC; 
``` 
>This query uses two inner join between the three events table. Using an inner join will only pull matching results between the tables. An order by statement is used to put our results into descending order. 

  **Output** 
  
  ![This is a image](https://imgur.com/QgW7uIs.png)
  >The result of this query shows us a trend between an event insurance plan and the attendees it has. What we can see is that liability and Cancellation Insurance is typically used with larger events. This gives us an idea of how different plans are being used. 

   **Query 3** 
  
```tsql 
-- Finding insurance plans based on deductibles 
SELECT i.i_type "Insurance Plan", TRUC(AVG(v.selling_price),2) AS "AVG Item Selling Price", TRUC(AVG(i.i_deductibles),2) AS "AVG deductible"
FROM valuable_items v JOIN item_policy p 
ON (v.item_id=p.item_id) 
JOIN item_insurance i 
ON (i.item_insurance_id=p.item_insurance_id) 
GROUP BY i.i_type 
HAVING AVG(i.i_deductibles)>='&deductible' 
``` 
>:warning: Note: this query is specific to Oracle SQL Developer. The following query will prompt user input through the "&" symbol. 

  **Output** 
  
   ![This is a image](https://imgur.com/wiQrSaN.png)
   
   ![This is a image](https://imgur.com/XLNlSm2.png)
> This query used a double join to pull outputs from the 3 valuable items table. The query will ask for user input for a deductible amount. In the query, I ran I input 100 meaning this query will only show insurance plans that have an average deductible of $100. 

   **Query 4** 

```tsql
-- Finding customer information based on coverage start and date 
SELECT c.l_name AS "Last Name", c.customer_id AS "Customer ID", c.DOB, c.gender, t.Destination 
FROM customer c LEFT JOIN travel_polciy p 
ON (c.customer_id=p.customer_id) 
LEFT JOIN travel t 
ON (t.zip_code=p.zip_code) 
WHERE p.t_coverage_start_date BETWEEN '&DATE1' AND '&DATE2' 
ORDER BY c.l_name; 
```
>:warning: Note: this query is specific to Oracle SQL Developer. The following query will prompt user input through the "&" symbol. 

  **Output** 
  
  ![This is a image](https://imgur.com/VKRCgCp.png)
  ![This is a image](https://imgur.com/mbsqj3N.png)
  ![This is a image](https://imgur.com/gOfWLG1.png)
  
  This query followed query 3 by asking the user for a prompt, however it takes it a step further and asks for additional input. This is because we are using the between the function to create a range between two dates. This query produces results to show customer information based on their travel coverage start and end date.
 
## Final Thoughts 

Overall many trials and errors when trying to get everything working, but I'm happy with this current product. My goal future is to come back to this in the future with additional tools make improvements to the overall tables and create more efficient SQL scripts. I truly appreciate anyone who managed to make it to the end of this, I hope to present more projects on here soon. 
