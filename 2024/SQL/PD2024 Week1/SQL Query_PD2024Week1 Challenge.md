**2024: Week 1 - Prep Air's Flow Card**
-------------------

January 03, 2024  
Challenge by: Carl Allchin

At Preppin' Data we use a number of (mock) companies to look at the challenges they have with their data. For January, we're going to focus on our own airline, Prep Air. The airline has introduced a new loyalty card called the Flow Card. We need to clean up a number of data sets to determine how well the card is doing. 

The first task is setting some context for later weeks by understanding how popular the Flow Card is. Our stakeholder would like two data sets about our passengers. One data set for card users and one data set for those who don't use the card. 


**Requirements**
- Input the data
- Split the Flight Details field to form:
  - Date 
  - Flight Number
  - From
  - To
  - Class
  - Price
- Convert the following data fields to the correct data types:
  - Date to a date format
  - Price to a decimal value
- Change the Flow Card field to Yes / No values instead of 1 / 0
- Create two tables, one for Flow Card holders and one for non-Flow Card holders
- Output the data sets
*The data source is in the PD2024Week1 folder*
  <br>
  <br>

**SQL Solution** (*In Snowflake*)  
Flow Card Holders
         
    WITH 
    Table1 AS
        (SELECT split_part(FLIGHT_DETAILS, '//', 1) AS Date_,
            split_part(FLIGHT_DETAILS, '//', 2) AS Flight_Number,
            split_part(FLIGHT_DETAILS, '//', 3) AS From_,
            split_part(FLIGHT_DETAILS, '//', 4) AS Class,
            split_part(FLIGHT_DETAILS, '//', 5) AS Price,
            FLOW_CARD,
            BAGS_CHECKED,
            MEAL_TYPE
        FROM PD2024W1
        )
      
    SELECT DATE_,
        FLIGHT_NUMBER,
        split_part(FROM_, '-', 1) AS FROM_,
        split_part(FROM_, '-', 2) AS TO_,
        CLASS,
        ROUND(PRICE,2) AS PRICE,
        CASE FLOW_CARD WHEN 1 THEN 'Yes' WHEN 0 THEN 'No' END AS FLOW_CARD_STR,
        BAGS_CHECKED,
        MEAL_TYPE 
    FROM Table1
    WHERE FLOW_CARD_STR = 'Yes'
    Limit 10 - --Limiting the table to 10 records as the table is large
    ;
  
**Result 1** (*The result is limited to 10 records*)
|DATE_|FLIGHT_NUMBER         |FROM_   |TO_     |CLASS          |PRICE  |FLOW_CARD_STR|BAGS_CHECKED|MEAL_TYPE |
|-----|----------------------|--------|--------|---------------|-------|-------------|------------|----------|
|2024-07-22|PA010                 |Tokyo   |New York|Economy        |2380.00|Yes          |0           |Egg Free  |
|2024-04-20|PA002                 |New York|London  |Economy        |3490.00|Yes          |1           |Vegan     |
|2024-01-23|PA010                 |Tokyo   |New York|Premium Economy|825.00 |Yes          |1           |Vegetarian|
|2024-06-05|PA006                 |Tokyo   |London  |First Class    |618.00 |Yes          |3           |Vegan     |
|2024-03-30|PA004                 |Perth   |London  |First Class    |446.00 |Yes          |1           |Nut Free  |
|2024-06-14|PA006                 |Tokyo   |London  |Premium Economy|1062.50|Yes          |3           |Egg Free  |
|2024-07-15|PA006                 |Tokyo   |London  |First Class    |499.00 |Yes          |1           |Egg Free  |
|2024-02-25|PA004                 |Perth   |London  |Premium Economy|827.50 |Yes          |0           |Vegetarian|
|2024-07-26|PA004                 |Perth   |London  |Economy        |2245.00|Yes          |1           |Vegan     |
|2024-12-10|PA004                 |Perth   |London  |Business Class |391.20 |Yes          |2           |Vegetarian|


<br>
<br>

**Non Flow Card Holders**

    WITH 
    Table1 AS
        (SELECT split_part(FLIGHT_DETAILS, '//', 1) AS Date_,
            split_part(FLIGHT_DETAILS, '//', 2) AS Flight_Number,
            split_part(FLIGHT_DETAILS, '//', 3) AS From_,
            split_part(FLIGHT_DETAILS, '//', 4) AS Class,
            split_part(FLIGHT_DETAILS, '//', 5) AS Price,
            FLOW_CARD,
            BAGS_CHECKED,
            MEAL_TYPE
        FROM PD2024W1
        )
      
    SELECT DATE_,
        FLIGHT_NUMBER,
        split_part(FROM_, '-', 1) AS FROM_,
        split_part(FROM_, '-', 2) AS TO_,
        CLASS,
        ROUND(PRICE,2) AS PRICE,
        CASE FLOW_CARD WHEN 1 THEN 'Yes' WHEN 0 THEN 'No' END AS FLOW_CARD_STR,
        BAGS_CHECKED,
        MEAL_TYPE 
    FROM Table1
    WHERE FLOW_CARD_STR = 'No'
    Limit 10
    ;

**Result 2**  (*The result is limited to 10 records*)
|DATE_|FLIGHT_NUMBER         |FROM_   |TO_     |CLASS          |PRICE  |FLOW_CARD_STR|BAGS_CHECKED|MEAL_TYPE |
|-----|----------------------|--------|--------|---------------|-------|-------------|------------|----------|
|2024-09-28|PA008                 |Perth   |New York|Economy        |1855.00|No           |2           |Vegetarian|
|2024-10-01|PA008                 |Perth   |New York|Business Class |634.80 |No           |0           |Vegetarian|
|2024-03-04|PA007                 |New York|Perth   |Business Class |458.40 |No           |3           |Nut Free  |
|2024-02-25|PA010                 |Tokyo   |New York|Premium Economy|1435.00|No           |0           |None      |
|2024-03-29|PA004                 |Perth   |London  |Economy        |2730.00|No           |2           |Vegan     |
|2024-06-07|PA005                 |London  |Tokyo   |Business Class |294.00 |No           |2           |Egg Free  |
|2024-06-30|PA009                 |New York|Tokyo   |First Class    |236.00 |No           |1           |Vegetarian|
|2024-08-12|PA011                 |Perth   |Tokyo   |Economy        |2570.00|No           |2           |Vegan     |
|2024-11-05|PA010                 |Tokyo   |New York|First Class    |397.00 |No           |2           |None      |
|2024-03-19|PA003                 |London  |Perth   |First Class    |585.00 |No           |2           |Egg Free  |

<br>
