**2023: Week 3 - Targets for DSB**
-------------------

January 18, 2023  
Challenge by: Jenny Martin  

For the third week of beginner month, we're going to be building on the skills that we've already learnt, as well as exploring new concepts. This week may feel a little more challenging, but I promise you're ready for it!
Data Source Bank has some quarterly targets for the value of transactions that are being performed in-person and online. It's our job to compare the transactions to these target figures.


**Requirements**
- Input the data
- For the transactions file:
  - Filter the transactions to just look at DSB 
    - These will be transactions that contain DSB in the Transaction Code field
  - Rename the values in the Online or In-person field, Online of the 1 values and In-Person for the 2 values
  - Change the date to be the quarter 
  - Sum the transaction values for each quarter and for each Type of Transaction (Online or In-Person) 
- For the targets file:
  - Pivot the quarterly targets so we have a row for each Type of Transaction and each Quarter 
  - Rename the fields
  - Remove the 'Q' from the quarter field and make the data type numeric 
- Join the two datasets together 
  - You may need more than one join clause!
- Remove unnecessary fields
- Calculate the Variance to Target for each row 
- Output the data  
*The data source is in the **PD2023Week3** folder*
  <br>
  <br>



**SQL Solution** (*In Snowflake*)  


    WITH    CTE1 AS 
            (
            SELECT * 
            FROM PD2023_WK03_TARGETS
            UNPIVOT(TARGETS FOR QUARTER IN (Q1, Q2, Q3, Q4))
            ),

            CTE2 AS
            (
            SELECT ONLINE_OR_IN_PERSON,
                RIGHT(QUARTER, 1) AS QUARTER,
                TARGETS
            FROM CTE1      
            ),
    
            CTE3 AS
            (
            SELECT 
            CASE
                WHEN ONLINE_OR_IN_PERSON = 1 THEN 'Online'
                WHEN ONLINE_OR_IN_PERSON = 2 THEN 'In-Person'
            END AS ONLINE_OR_IN_PERSON,
            QUARTER(TO_DATE(TRANSACTION_DATE, 'DD/MM/YYYY HH:MI:SS')) AS QUARTER,
            SUM(VALUE) AS VALUE
            FROM PD2023_WK01
            WHERE CONTAINS(TRANSACTION_CODE, 'DSB')
            GROUP BY ONLINE_OR_IN_PERSON, QUARTER
            )
    
    SELECT A.ONLINE_OR_IN_PERSON,
        A.QUARTER,
        A.VALUE,
        B.TARGETS AS QUARTERLY_TARGETS,
        (SUM(A.VALUE) - SUM(B.TARGETS)) AS VARIANCE_TO_TARGET
    FROM CTE3 AS A
    INNER JOIN CTE2 AS B
        ON A.ONLINE_OR_IN_PERSON = B.ONLINE_OR_IN_PERSON
        AND A.QUARTER = B.QUARTER
    GROUP BY A.ONLINE_OR_IN_PERSON,
        A.QUARTER,
        A.VALUE,
        QUARTERLY_TARGETS
    ;
        
**Result** 
|ONLINE_OR_IN_PERSON|QUARTER|VALUE|QUARTERLY_TARGETS|VARIANCE_TO_TARGET|
|-------------------|-------|-----|-----------------|------------------|
|Online             |1      |74562|72500            |2062              |
|Online             |2      |69325|70000            |-675              |
|Online             |3      |59072|60000            |-928              |
|Online             |4      |61908|60000            |1908              |
|In-Person          |1      |77576|75000            |2576              |
|In-Person          |2      |70634|70000            |634               |
|In-Person          |4      |43223|60000            |-16777            |
|In-Person          |3      |74189|70000            |4189              |


<br>
<br>

