# Finding candidates having 3 specific skills using WHERE, GROUP BY and HAVING
Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL.

Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.

Assumption:  There are no duplicates in the candidates table.
    
    
<div style="display: flex; justify-content: center;">
  <div style="flex: 1; margin-right: 10px;">
      candidates
<table ><thead><tr><th style="text-align:left"><strong>Column Name</strong></th><th style="text-align:left"><strong>Type</strong></th></tr></thead><tbody><tr><td style="text-align:left">candidate_id</td><td style="text-align:left">integer</td></tr><tr><td style="text-align:left">skill</td><td style="text-align:left">varchar</td></tr></tbody></table></div>

  <div style="flex: 1; margin-right: 10px;">
 Example Input
<table><thead><tr><th style="text-align: left;"><strong>candidate_id</strong></th><th style="text-align: left;"><strong>skill</strong></th></tr></thead><tbody><tr><td style="text-align: left;">123</td><td style="text-align: left;">Python</td></tr><tr><td style="text-align: left;">123</td><td style="text-align: left;">Tableau</td></tr><tr><td style="text-align: left;">123</td><td style="text-align: left;">PostgreSQL</td></tr><tr><td style="text-align: left;">234</td><td style="text-align: left;">R</td></tr><tr><td style="text-align: left;">234</td><td style="text-align: left;">PowerBI</td></tr><tr><td style="text-align: left;">234</td><td style="text-align: left;">SQL Server</td></tr><tr><td style="text-align: left;">345</td><td style="text-align: left;">Python</td></tr><tr><td style="text-align: left;">345</td><td style="text-align: left;">Tableau</td></tr></tbody></table>
</div>
     <div style="flex: 1; margin-right: 10px;">
 Example Output

<table><thead><tr><th style="text-align: left;"><strong>candidate_id</strong></th></tr></thead><tbody><tr><td style="text-align: left;">123</td></tr></tbody></table>
    </div>
    
     

My solution using common table expression:


```python
With datascienceskills as
  (SELECT c.candidate_id,
        CASE when EXISTS (SELECT * from candidates where candidate_id=c.candidate_id and skill='Python') then 1 else 0 end as python,
        CASE when EXISTS (SELECT * from candidates where candidate_id=c.candidate_id and skill='Tableau') then 1 else 0 end as tableau,
        CASE when EXISTS (SELECT * from candidates where candidate_id=c.candidate_id and skill='PostgreSQL') then 1 else 0 end as postgresql
  from candidates as c)
     
SELECT DISTINCT candidate_id from datascienceskills
where python=1 and tableau=1 and postgresql=1
ORDER BY candidate_id;
```

Considering there is no duplicate in the table, the question could be solved using WHERE and GROUP BY:


```python
SELECT candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(skill) = 3
ORDER BY candidate_id;
```

# Finding pages with no like using EXCEPT (PostgreSQL) or MINUS (MySQL and Oracle)

Assume you are given the tables below about Facebook pages and page likes. Write a query to return the page IDs of all the Facebook pages that don't have any likes. The output should be in ascending order.


<div style="display: flex; justify-content: center;">
  <div style="flex: 1; margin-right: 10px;">
pages:
<table><thead><tr><th style="text-align:left"><strong>Column Name</strong></th><th style="text-align:left"><strong>Type</strong></th></tr></thead><tbody><tr><td style="text-align:left">page_id</td><td style="text-align:left">integer</td></tr><tr><td style="text-align:left">page_name</td><td style="text-align:left">varchar</td></tr></tbody></table>
    </div>
    
 <div style="flex: 1; margin-right: 10px;">
page_likes
<table><thead><tr><th style="text-align:left"><strong>Column Name</strong></th><th style="text-align:left"><strong>Type</strong></th></tr></thead><tbody><tr><td style="text-align:left">user_id</td><td style="text-align:left">integer</td></tr><tr><td style="text-align:left">page_id</td><td style="text-align:left">integer</td></tr><tr><td style="text-align:left">liked_date</td><td style="text-align:left">datetime</td></tr></tbody></table>
    </div>
        </div>
        
        

<div style="display: flex; justify-content: center;">
  <div style="flex: 1; margin-right: 10px;">
pages example:

<table><thead><tr><th style="text-align:left"><strong>page_id</strong></th><th style="text-align:left"><strong>page_name</strong></th></tr></thead><tbody><tr><td style="text-align:left">20001</td><td style="text-align:left">SQL Solutions</td></tr><tr><td style="text-align:left">20045</td><td style="text-align:left">Brain Exercises</td></tr><tr><td style="text-align:left">20701</td><td style="text-align:left">Tips for Data Analysts</td></tr></tbody></table>
    </div>
    
 <div style="flex: 1; margin-right: 10px;">
page_likes example:

<table><thead><tr><th style="text-align:left"><strong>user_id</strong></th><th style="text-align:left"><strong>page_id</strong></th><th style="text-align:left"><strong>liked_date</strong></th></tr></thead><tbody><tr><td style="text-align:left">111</td><td style="text-align:left">20001</td><td style="text-align:left">04/08/2022 00:00:00</td></tr><tr><td style="text-align:left">121</td><td style="text-align:left">20045</td><td style="text-align:left">03/12/2022 00:00:00</td></tr><tr><td style="text-align:left">156</td><td style="text-align:left">20001</td><td style="text-align:left">07/25/2022 00:00:00</td></tr></tbody></table>
         </div>
    
<div style="flex: 1; margin-right: 10px;">
Example output:
<table><thead><tr><th style="text-align:left"><strong>page_id</strong></th></tr></thead><tbody><tr><td style="text-align:left">20701</td></tr></tbody></table>
        </div>
        </div>





```python
SELECT page_id FROM pages
EXCEPT
SELECT DISTINCT page_id FROM page_likes
ORDER BY page_id;
```

# Finding distinct products with null values using DISTINCT or GROUP BY

Tesla is investigating bottlenecks in their production, and they need your help to extract the relevant data. Write a query that determines which parts have begun the assembly process but are not yet finished.

Assumptions

    Table parts_assembly contains all parts in production.
    A part with no finish_date is an unfinished part.
    
    
<div style="display: flex; justify-content: center;">
<div style="flex: 1; margin-right: 10px;">
    parts_assembly
    <table><thead><tr><th style="text-align:left">Column Name</th><th style="text-align:left">Type</th></tr></thead><tbody><tr><td style="text-align:left">part</td><td style="text-align:left">string</td></tr><tr><td style="text-align:left">finish_date</td><td style="text-align:left">datetime</td></tr><tr><td style="text-align:left">assembly_step</td><td style="text-align:left">integer</td></tr></tbody></table>
</div>

<div style="flex: 1; margin-right: 10px;">
    parts_assembly example
    <table><thead><tr><th style="text-align:left">part</th><th style="text-align:left">finish_date</th><th style="text-align:left">assembly_step</th></tr></thead><tbody><tr><td style="text-align:left">battery</td><td style="text-align:left">01/22/2022 00:00:00</td><td style="text-align:left">1</td></tr><tr><td style="text-align:left">battery</td><td style="text-align:left">02/22/2022 00:00:00</td><td style="text-align:left">2</td></tr><tr><td style="text-align:left">battery</td><td style="text-align:left">03/22/2022 00:00:00</td><td style="text-align:left">3</td></tr><tr><td style="text-align:left">bumper</td><td style="text-align:left">01/22/2022 00:00:00</td><td style="text-align:left">1</td></tr><tr><td style="text-align:left">bumper</td><td style="text-align:left">02/22/2022 00:00:00</td><td style="text-align:left">2</td></tr><tr><td style="text-align:left">bumper</td><td style="text-align:left"></td><td style="text-align:left">3</td></tr><tr><td style="text-align:left">bumper</td><td style="text-align:left"></td><td style="text-align:left">4</td></tr></tbody></table>
                                  </div>

    
<div style="flex: 1; margin-right: 10px;">
Example output:
<table><thead><tr><th style="text-align:left">part</th></tr></thead><tbody><tr><td style="text-align:left">bumper</td></tr></tbody></table>
</div>
</div>

My solution was


```python
SELECT DISTINCT part FROM parts_assembly
WHERE finish_date is NULL;
```

Alternatively, GROUP BY could be used


```python
SELECT part
FROM parts_assembly
WHERE finish_date IS NULL
GROUP BY part;
```

# Finding laptop vs. mobile views using SUM and CASE
Assume that you are given the table below containing information on viewership by device type (where the three types are laptop, tablet, and phone). Define “mobile” as the sum of tablet and phone viewership numbers. Write a query to compare the viewership on laptops versus mobile devices.

Output the total viewership for laptop and mobile devices in the format of "laptop_views" and "mobile_views".

<div style="display: flex; justify-content: center;">
<div style="flex: 1; margin-right: 10px;">
viewership table:
<table><thead><tr><th style="text-align:left">Column Name</th><th style="text-align:left">Type</th></tr></thead><tbody><tr><td style="text-align:left">user_id</td><td style="text-align:left">integer</td></tr><tr><td style="text-align:left">device_type</td><td style="text-align:left">string ('laptop', 'tablet', 'phone')</td></tr><tr><td style="text-align:left">view_time</td><td style="text-align:left">timestamp</td></tr></tbody></table>
</div>

<div style="flex: 1; margin-right: 10px;">
viewership example:
<table><thead><tr><th style="text-align:left">user_id</th><th style="text-align:left">device_type</th><th style="text-align:left">view_time</th></tr></thead><tbody><tr><td style="text-align:left">123</td><td style="text-align:left">tablet</td><td style="text-align:left">01/02/2022 00:00:00</td></tr><tr><td style="text-align:left">125</td><td style="text-align:left">laptop</td><td style="text-align:left">01/07/2022 00:00:00</td></tr><tr><td style="text-align:left">128</td><td style="text-align:left">laptop</td><td style="text-align:left">02/09/2022 00:00:00</td></tr><tr><td style="text-align:left">129</td><td style="text-align:left">phone</td><td style="text-align:left">02/09/2022 00:00:00</td></tr><tr><td style="text-align:left">145</td><td style="text-align:left">tablet</td><td style="text-align:left">02/24/2022 00:00:00</td></tr></tbody></table>
</div>

<div style="flex: 1; margin-right: 10px;">
Example output:
<table><thead><tr><th style="text-align:left">laptop_views</th><th style="text-align:left">mobile_views</th></tr></thead><tbody><tr><td style="text-align:left">2</td><td style="text-align:left">3</td></tr></tbody></table>
</div>
</div>

One can get the number of views using GROUP By. However this table would not look like the example output above


```python
SELECT device_type, COUNT(view_time) 
FROM viewership 
GROUP BY device_type
```

To get the columns wanted, one can sum over newly defined columns as below:


```python
SELECT SUM(CASE WHEN device_type = 'laptop' THEN 1 ELSE 0 END) as laptop_views,
SUM(CASE WHEN device_type = 'tablet' OR device_type='phone' THEN 1 ELSE 0 END) as mobile_views
from viewership

```

# Finding the third transaction of each user using CTE and window function (ROW_NUMVER(), PARTITION BY, ORDER BY)

Assume you are given the table below on Uber transactions made by users. Write a query to obtain the third transaction of every user. Output the user id, spend and transaction date.

<div style="display: flex; justify-content: center;">
<div style="flex: 1; margin-right: 10px;">
transactions table:
<table><thead><tr><th style="text-align:left"><strong>Column Name</strong></th><th style="text-align:left"><strong>Type</strong></th></tr></thead><tbody><tr><td style="text-align:left">user_id</td><td style="text-align:left">integer</td></tr><tr><td style="text-align:left">spend</td><td style="text-align:left">decimal</td></tr><tr><td style="text-align:left">transaction_date</td><td style="text-align:left">timestamp</td></tr></tbody></table>
</div>

<div style="flex: 1; margin-right: 10px;">
transactions example:
<table><thead><tr><th style="text-align:left"><strong>user_id</strong></th><th style="text-align:left"><strong>spend</strong></th><th style="text-align:left"><strong>transaction_date</strong></th></tr></thead><tbody><tr><td style="text-align:left">111</td><td style="text-align:left">100.50</td><td style="text-align:left">01/08/2022 12:00:00</td></tr><tr><td style="text-align:left">111</td><td style="text-align:left">55.00</td><td style="text-align:left">01/10/2022 12:00:00</td></tr><tr><td style="text-align:left">121</td><td style="text-align:left">36.00</td><td style="text-align:left">01/18/2022 12:00:00</td></tr><tr><td style="text-align:left">145</td><td style="text-align:left">24.99</td><td style="text-align:left">01/26/2022 12:00:00</td></tr><tr><td style="text-align:left">111</td><td style="text-align:left">89.60</td><td style="text-align:left">02/05/2022 12:00:00</td></tr></tbody></table>
</div>

<div style="flex: 1; margin-right: 10px;">
Output example:
<table><thead><tr><th style="text-align:left"><strong>user_id</strong></th><th style="text-align:left"><strong>spend</strong></th><th style="text-align:left"><strong>transaction_date</strong></th></tr></thead><tbody><tr><td style="text-align:left">111</td><td style="text-align:left">89.60</td><td style="text-align:left">02/05/2022 12:00:00</td></tr></tbody></table>
</div>
</div>

My solution (this could alternatively be written using subquery instead of CTE)


```python
with indtransactions AS
(SELECT user_id,
        spend,
        transaction_date,
        ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY transaction_date) as row
  FROM transactions)

select user_id,
       spend,
       transaction_date
FROM indtransactions
WHERE row=3;
```

# Finding percentage of time users spent on send vs open using CTE, LEFT JOIN, GROUP BY, SUM, CASE and ROUND
Assume you are given the tables below containing information on Snapchat users, their ages, and their time spent sending and opening snaps. Write a query to obtain a breakdown of the time spent sending vs. opening snaps (as a percentage of total time spent on these activities) for each age group.

Output the age bucket and percentage of sending and opening snaps. Round the percentage to 2 decimal places.


<div style="display: flex; justify-content: center;">
<div style="flex: 1; margin-right: 10px;">
activities table:
<table><thead><tr><th style="text-align:left">Column Name</th><th style="text-align:left">Type</th></tr></thead><tbody><tr><td style="text-align:left">activity_id</td><td style="text-align:left">integer</td></tr><tr><td style="text-align:left">user_id</td><td style="text-align:left">integer</td></tr><tr><td style="text-align:left">activity_type</td><td style="text-align:left">string ('send', 'open', 'chat')</td></tr><tr><td style="text-align:left">time_spent</td><td style="text-align:left">float</td></tr><tr><td style="text-align:left">activity_date</td><td style="text-align:left">datetime</td></tr></tbody></table></div>

<div style="flex: 1; margin-right: 10px;">
activities example:
<table><thead><tr><th style="text-align:left">activity_id</th><th style="text-align:left">user_id</th><th style="text-align:left">activity_type</th><th style="text-align:left">time_spent</th><th style="text-align:left">activity_date</th></tr></thead><tbody><tr><td style="text-align:left">7274</td><td style="text-align:left">123</td><td style="text-align:left">open</td><td style="text-align:left">4.50</td><td style="text-align:left">06/22/2022 12:00:00</td></tr><tr><td style="text-align:left">2425</td><td style="text-align:left">123</td><td style="text-align:left">send</td><td style="text-align:left">3.50</td><td style="text-align:left">06/22/2022 12:00:00</td></tr><tr><td style="text-align:left">1413</td><td style="text-align:left">456</td><td style="text-align:left">send</td><td style="text-align:left">5.67</td><td style="text-align:left">06/23/2022 12:00:00</td></tr><tr><td style="text-align:left">1414</td><td style="text-align:left">789</td><td style="text-align:left">chat</td><td style="text-align:left">11.00</td><td style="text-align:left">06/25/2022 12:00:00</td></tr><tr><td style="text-align:left">2536</td><td style="text-align:left">456</td><td style="text-align:left">open</td><td style="text-align:left">3.00</td><td style="text-align:left">06/25/2022 12:00:00</td></tr></tbody></table></div></div>


<div style="display: flex; justify-content: center;">
<div style="flex: 1; margin-right: 10px;">
age_breakdown table:
<table><thead><tr><th style="text-align:left">Column Name</th><th style="text-align:left">Type</th></tr></thead><tbody><tr><td style="text-align:left">user_id</td><td style="text-align:left">integer</td></tr><tr><td style="text-align:left">age_bucket</td><td style="text-align:left">string ('21-25', '26-30', '31-25')</td></tr></tbody></table></div>


<div style="flex: 1; margin-right: 10px;">
age_breakdown example:
<table><thead><tr><th style="text-align:left">user_id</th><th style="text-align:left">age_bucket</th></tr></thead><tbody><tr><td style="text-align:left">123</td><td style="text-align:left">31-35</td></tr><tr><td style="text-align:left">456</td><td style="text-align:left">26-30</td></tr><tr><td style="text-align:left">789</td><td style="text-align:left">21-25</td></tr></tbody></table></div>

<div style="flex: 1; margin-right: 10px;">
Example output:
<table><thead><tr><th style="text-align:left">age_bucket</th><th style="text-align:left">send_perc</th><th style="text-align:left">open_perc</th></tr></thead><tbody><tr><td style="text-align:left">26-30</td><td style="text-align:left">65.40</td><td style="text-align:left">34.60</td></tr><tr><td style="text-align:left">31-35</td><td style="text-align:left">43.75</td><td style="text-align:left">56.25</td></tr></tbody></table>
</div>
</div>

My solution was:


```python
WITH sumopensend AS
(SELECT ag.age_bucket,
        SUM(CASE when ac.activity_type='send' THEN ac.time_spent else 0 end) as sumsend,
        SUM(CASE when ac.activity_type='open' THEN ac.time_spent else 0 end) as sumopen
FROM activities  as ac
LEFT JOIN age_breakdown as ag using (user_id) 
WHERE ac.activity_type IN ('send', 'open') 
GROUP BY ag.age_bucket)


SELECT age_bucket, 
        ROUND(sumsend/(sumsend+sumopen)*100.0,2) as send_perc,
         ROUND(sumopen/(sumsend+sumopen)*100.0,2) as open_perc
FROM  sumopensend

```
