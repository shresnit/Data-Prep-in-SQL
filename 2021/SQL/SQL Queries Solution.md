
**2021: Week 1**
-------------------
This week we are going to be focusing on cleaning data ready to answer some questions from our stakeholders. In the requirements I will be adding some links to some useful resources if you get stuck on a particular requirement. 

**Requirements**
- There are 1,000 orders.
- Connect and load the csv file
- Split the 'Store-Bike' field into 'Store' and 'Bike'
- Clean up the 'Bike' field to leave just three values in the 'Bike' field (Mountain, Gravel, Road)
- Create two different cuts of the date field: 'quarter' and 'day of month'
- Remove the first 10 orders as they are test values (help)
- Output the data as a csv 

*The data source is in the PD2021Week1 folder*
  <br>
  <br>

**__Solution__** (In Snowflake)

    WITH 
        CTE1 AS
            (SELECT ORDER_ID,
                CUSTOMER_AGE,
                BIKE_VALUE,
                EXISTING_CUSTOMER,
                DATE,
                SPLIT_PART(STORE_BIKE, '-',1) AS STORE,
                LEFT(SPLIT_PART(STORE_BIKE, '-',2), 3) AS BIKE
             FROM PD2021_WK01),
       
        CTE2 AS
            (SELECT QUARTER(DATE) AS Quarter,
                STORE,
                CASE
                    WHEN UPPER(TRIM(BIKE)) = 'RO' THEN 'Road'
                    WHEN UPPER(TRIM(BIKE)) = 'MO' THEN 'Mountain'
                    WHEN UPPER(TRIM(BIKE)) = 'GR' THEN 'Gravel'
                END AS BIKE,
                ORDER_ID,
                CUSTOMER_AGE,
                BIKE_VALUE,
                EXISTING_CUSTOMER,
                DAYOFMONTH(DATE) AS Day_of_Month
            FROM CTE1)
            
            
    SELECT * FROM CTE2
    WHERE ORDER_ID > 10
    
    Limit 10 --Limiting the table to 10 records as the table is large
    ;

**Result**  (*The result is limited to 10 records*)
|QUARTER|STORE      |BIKE    |ORDER_ID|CUSTOMER_AGE|BIKE_VALUE|EXISTING_CUSTOMER|DAY_OF_MONTH|
|-------|-----------|--------|--------|------------|----------|-----------------|------------|
|4      |Birmingham |Road    |11      |57          |902       |No               |4           |
|1      |Leeds      |Road    |12      |31          |946       |Yes              |17          |
|4      |Birmingham |Road    |13      |17          |1296      |Yes              |25          |
|3      |Manchester |Road    |14      |59          |1166      |Yes              |18          |
|4      |Manchester |Mountain|15      |24          |1781      |No               |10          |
|4      |York       |Mountain|16      |59          |1074      |No               |6           |
|3      |York       |Mountain|17      |57          |1188      |No               |14          |
|4      |York       |Mountain|18      |56          |544       |No               |23          |
|4      |York       |Gravel  |19      |34          |579       |Yes              |24          |
|2      |York       |Gravel  |20      |17          |1021      |Yes              |24          |


<br>
<br>
