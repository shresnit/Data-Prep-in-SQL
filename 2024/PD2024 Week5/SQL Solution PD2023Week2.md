**PD 2024 Week 5 - Getting the right data**
-------------------

January 31, 2024  
Challenge by: Jenny Martin  

It's the final week of beginner month and we're going to spend a little more time diving deeper into joins, calculations and outputs.   
<br>
Prep Air are interested in creating a workflow that has multiple outputs depending on user requirements. They want users to be able to answer the following questions:  
<br>
- What are the details of the customers who have booked flights and which routes are they travelling on?  
- Which customers are yet to book a flight in 2024?  
- Which flights are yet to be booked by customers in 2024?  
The datasets you'll be working with are fairly large so you'll need to decide which tables to join (and when) to be as efficient as possible. You may wish to use this as an opportunity to explore the sampling options in Tableau Prep too!  


**Requirements**
- Input the data
- For the first output:
  - Create a dataset that gives all the customer details for booked flights in 2024. Make sure the output also includes details on the flights origin and destination
  - When outputting the data, create an excel file with a new sheet for each output (so 1 file for all outputs this week!)
- For the second output:
  - Create a dataset that allows Prep Air to identify which flights have not yet been booked in 2024
  - Add a datestamp field to this dataset for today's date (31/01/2024) so that Prep Air know the unbooked flights as of the day the workflow is run
  - When outputting the table to a new sheet in the Excel Workbook, choose the option "Append to Table" under Write Options. This means that if the workflow is run on a different day, the results will add additional rows to the dataset, rather than overwriting the previous run's data
- For the third output:
  - Create a dataset that shows which customers have yet to book a flight with Prep Air in 2024
  - Create a field which will allow Prep Air to see how many days it has been since the customer last flew (compared to 31/01/2024)
  - Categorise customers into the following groups:
    - Recent fliers - flown within the last 3 months
    - Taking a break - 3-6 months since last flight
    - Been away a while - 6-9 months since last flight
    - Lapsed Customers - over 9 months since last flight
  - Output the data to a new sheet in the Excel Workbook

*The data source is in the **PD Week5** folder*
  <br>
  <br>



**First Output**  

<br>  

**SQL Solution** (*In Snowflake*)  


    SELECT C.DATE,
        C.FROM_,
        C.TO_,
        C.FLIGHT_NUMBER,
        A.CUSTOMER_ID,
        A.LAST_DATE_FLOWN,
        A.first_name,
        A.LAST_NAME,
        A.EMAIL,
        A.GENDER,
        B.TICKET_PRICE
        
    FROM PD2024W5_PREPAIR_CUSTOMERS AS A
    INNER JOIN PD2024W5_PREPAIR_TICKETSALES AS B
        ON A.CUSTOMER_ID = B.CUSTOMER_ID
    INNER JOIN PD2024W5_PREPAIR_FLIGHTS AS C
        ON B.DATE_ = C.DATE AND B.FLIGHT_NUMBER = C.FLIGHT_NUMBER
    LIMIT 5 -- Limited to 5 records as the output is large
    ;
        
**Ouput**  
|DATE      |FROM_ |TO_     |FLIGHT_NUMBER|CUSTOMER_ID|LAST_DATE_FLOWN|FIRST_NAME|LAST_NAME|EMAIL                     |GENDER|
|----------|------|--------|-------------|-----------|---------------|----------|---------|--------------------------|------|
|2024-01-03|London|New York|PA001        |232        |2024-01-03     |Arv       |Ballinger|aballinger6f@hostgator.com|Male  |
|2024-01-03|London|New York|PA001        |293        |2024-01-03     |Corney    |Arger    |carger84@mac.com          |Male  |
|2024-01-03|London|New York|PA001        |472        |2024-01-03     |Katy      |Akram    |kakramd3@marketwatch.com  |Female|
|2024-01-03|London|New York|PA001        |572        |2024-01-03     |Jocko     |Bolwell  |jbolwellfv@hubpages.com   |Male  |
|2024-01-03|London|New York|PA001        |1191       |2024-01-03     |Gennie    |Head     |ghead5a@imageshack.us     |Female|
<br>
<br>

**Second Output**  

<br>  

**SQL Solution** (*In Snowflake*)  


    SELECT C.DATE,
        C.FLIGHT_NUMBER,
        C.FROM_,
        C.TO_
    FROM PD2024W5_PREPAIR_FLIGHTS AS C
    LEFT OUTER JOIN PD2024W5_PREPAIR_TICKETSALES AS B
        ON B.DATE_ = C.DATE AND B.FLIGHT_NUMBER = C.FLIGHT_NUMBER
    WHERE B.FLIGHT_NUMBER IS NULL
    LIMIT 5 -- Limited to 5 records as the output is large
    ;
            
**Ouput** 
|DATE      |FLIGHT_NUMBER|FROM_   |TO_  |
|----------|-------------|--------|-----|
|2024-12-11|PA005        |London  |Tokyo|
|2024-11-22|PA008        |Perth   |New York|
|2024-12-03|PA002        |New York|London|
|2024-12-11|PA004        |Perth   |London|
|2024-12-11|PA008        |Perth   |New York|
<br>
<br>

**Third Output**  

<br>  

**SQL Solution** (*In Snowflake*)  


        SELECT A.CUSTOMER_ID,
                  DATEDIFF('day', LAST_DATE_FLOWN, DATE('2024-1-31')) AS DAYS_SINCE_LAST_FLOWN,
                  
                  CASE
                      WHEN DAYS_SINCE_LAST_FLOWN <= 90 THEN 'Recent Fliers'
                      WHEN DAYS_SINCE_LAST_FLOWN > 90 AND DAYS_SINCE_LAST_FLOWN <= 180 THEN 'Taking a break'
                      WHEN DAYS_SINCE_LAST_FLOWN > 180 AND DAYS_SINCE_LAST_FLOWN <= 270 THEN 'Been away a while'
                      WHEN DAYS_SINCE_LAST_FLOWN > 270 THEN 'Lapsed Customers'
                  END AS CUSTOMER_CATEGORY,
          
                  LAST_DATE_FLOWN,
                  FIRST_NAME,
                  LAST_NAME,
                  EMAIL,
                  GENDER
        
        FROM PD2024W5_PREPAIR_CUSTOMERS AS A
        LEFT OUTER JOIN PD2024W5_PREPAIR_TICKETSALES AS B
            ON A.CUSTOMER_ID = B.CUSTOMER_ID
        WHERE FLIGHT_NUMBER IS NULL
        LIMIT 5 -- Limited to 5 records as the output is large
        ;
        
**Ouput** 
|CUSTOMER_ID|DAYS_SINCE_LAST_FLOWN|CUSTOMER_CATEGORY|LAST_DATE_FLOWN|FIRST_NAME|LAST_NAME     |EMAIL                          |GENDER     |
|-----------|---------------------|-----------------|---------------|----------|--------------|-------------------------------|-----------|
|6714       |338                  |Lapsed Customers |2023-02-27     |Isaac     |Cubbit        |icubbitjt@stanford.edu         |Male       |
|3528       |180                  |Taking a break   |2023-08-04     |Yank      |Iorizzi       |yiorizzien@google.ru           |Male       |
|711        |165                  |Taking a break   |2023-08-19     |Alden     |De Normanville|adenormanvillejq@guardian.co.uk|Bigender   |
|3212       |104                  |Taking a break   |2023-10-19     |Tresa     |Skitteral     |tskitteral5v@berkeley.edu      |Genderfluid|
|471        |354                  |Lapsed Customers |2023-02-11     |Quill     |Eatock        |qeatockd2@hubpages.com         |Male       |


<br>
<br>




