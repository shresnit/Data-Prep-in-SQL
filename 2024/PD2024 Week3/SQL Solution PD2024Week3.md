**2024: Week 3 Performance Against Targets**
-------------------

January 17, 2024  
Created by: Carl Allchin     

The world is swimming in data and with so many sources everywhere, it's often on you to tie them together. Most data tools need to read from a single data so will use Unions, Joins and Logical Relationship models to tie them together. In this week's challenge we will introduce you to joining data sets together to prepare them for analysis. 

This week's challenge is to link together a Quarterly Sales Target data source (an Excel Workbook) with our original sales data (Week One output). Is Prep Air meeting its targets?


**Requirements**
- Input the outputs from 2024 Week 1 challenge
- Input the new targets Excel sheet (Q1 - 4) 
- Correct the Classes being incorrect as per last week
  - Economy to First
  - First Class to Economy
  - Business Class to Premium
  - Premium Economy to Business
- Find the First Letter from each word in the Class to help with joining the Targets data to Sales data
- Change the date to a month number 
- Total up the sales at the level of:
  - Class
  - Month
- Join the Targets data on to the Sales data (note - you should have 48 rows of data after the join)
- Calculate the difference between the Sales and Target values per Class and Month
- Output the data

*The data source is in the **PD2024 Week3** folder*
  <br>
  <br>


**SQL Solution** (*In Snowflake*)  


        WITH 
    
            CTE1 AS (SELECT * FROM PD2024_WK1_OUTPUT_FLOW_CARD
                    UNION
                    SELECT * FROM PD2024_WK1_OUTPUT_NONFLOWCARD),
        
            CTE2 AS (SELECT *,
                        CASE
                        WHEN CLASS = 'Economy' THEN 'First'
                        WHEN CLASS = 'First Class' THEN 'Economy'
                        WHEN CLASS = 'Business Class' THEN 'Premium'
                        WHEN CLASS = 'Premium Economy' THEN 'Business'
                        END AS CLASS_RENAME,
                        MONTH(TO_DATE(DATE, 'DD/MM/YYYY')) AS MONTH,
                        LEFT(CLASS_RENAME, 1) AS FIRSTLETTER
                      FROM CTE1),
        
            CTE3 AS (SELECT MONTH AS DATE,
                        SUM(PRICE) AS PRICE,
                        CLASS_RENAME AS CLASS,
                        FIRSTLETTER
                    FROM CTE2
                    GROUP BY MONTH, CLASS_RENAME, FIRSTLETTER
                    ),
        
           CTE4 AS (SELECT * FROM PD2024_W3_INPUT_Q1
                    UNION
                    SELECT * FROM PD2024_W3_INPUT_Q2
                    UNION
                    SELECT * FROM PD2024_W3_INPUT_Q3
                    UNION
                    SELECT * FROM PD2024_W3_INPUT_Q4
                    ),
        
          CTE5 AS (SELECT CTE3.DATE AS DATE,
                        CTE3.PRICE AS PRICE,
                        CTE4.CLASS AS CLASS,
                        CTE4.TARGET AS TARGET
                 FROM CTE3
                 INNER JOIN CTE4
                    ON CTE3.FIRSTLETTER = LEFT(CTE4.CLASS, 1) AND CTE3.DATE = CTE4.MONTH
                  ),
        
         CTE6 AS (SELECT (PRICE - TARGET) AS DIFFERENCE_TO_TARGET,
                        DATE,
                        PRICE,
                        CLASS,
                        TARGET
                    FROM CTE5
                    GROUP BY DATE, PRICE, CLASS, TARGET
                  )
    
    SELECT * FROM CTE6
    ;
        
**Output** 
|DIFFERENCE_TO_TARGET|DATE|PRICE |CLASS|TARGET|
|--------------------|----|------|-----|------|
|73960               |1   |193960|FC   |120000|
|-17686              |1   |67314 |BC   |85000 |
|-16759              |2   |69241 |BC   |86000 |
|-2624               |3   |84376 |BC   |87000 |
|5836                |2   |46336 |PE   |40500 |
|6871                |3   |47871 |PE   |41000 |
|5081                |1   |36081 |E    |31000 |
|-171                |3   |31829 |E    |32000 |
|-532                |2   |30968 |E    |31500 |
|12315               |3   |152315|FC   |140000|
|64415               |4   |224415|FC   |160000|
|3036                |6   |46536 |PE   |43500 |
|-2667               |4   |31333 |E    |34000 |
|-17575              |7   |172425|FC   |190000|
|-4947               |9   |42053 |PE   |47000 |
|-51985              |12  |188015|FC   |240000|
|9625                |8   |209625|FC   |200000|
|-7391               |9   |87609 |BC   |95000 |
|3588                |12  |43588 |E    |40000 |
|833                 |5   |43833 |PE   |43000 |
|-3919               |5   |85081 |BC   |89000 |
|1610                |5   |171610|FC   |170000|
|-13013              |11  |85987 |BC   |99000 |
|-3560               |6   |31440 |E    |35000 |
|13878               |8   |106878|BC   |93000 |
|-9127               |11  |39373 |PE   |48500 |
|-6459               |4   |81541 |BC   |88000 |
|5516                |7   |97516 |BC   |92000 |
|-4680               |8   |32820 |E    |37500 |
|-5477               |11  |34023 |E    |39500 |
|-33815              |10  |186185|FC   |220000|
|15665               |2   |145665|FC   |130000|
|8555                |1   |48555 |PE   |40000 |
|-4249               |6   |85751 |BC   |90000 |
|-9040               |9   |200960|FC   |210000|
|-30275              |6   |149725|FC   |180000|
|-5738               |7   |40262 |PE   |46000 |
|-4311               |10  |43689 |PE   |48000 |
|506                 |12  |100506|BC   |100000|
|-4268               |4   |38232 |PE   |42500 |
|-6500               |10  |32500 |E    |39000 |
|8398                |5   |42898 |E    |34500 |
|-10276              |9   |27724 |E    |38000 |
|-5035               |8   |41465 |PE   |46500 |
|-3843               |10  |94157 |BC   |98000 |
|-3655               |12  |45345 |PE   |49000 |
|1941                |7   |38941 |E    |37000 |
|-75405              |11  |154595|FC   |230000|


<br>
<br>

