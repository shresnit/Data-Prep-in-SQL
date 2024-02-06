**2024: Week 2 - Average Price Analysis**
-------------------

January 10, 2024  
Created by: Carl Allchin  

It's the second week of our introductory challenges. This week the challenge will involve unions, aggregation and reshaping data.


**Requirements**
- Input the two csv files
- Union the files together
- Convert the Date field to a Quarter Number instead
  - Name this field Quarter
- Aggregate the data in the following ways:
  - Median price per Quarter, Flow Card? and Class
  - Minimum price per Quarter, Flow Card? and Class
  - Maximum price per Quarter, Flow Card? and Class
- Create three separate flows where you have only one of the aggregated measures in each. 
  - One for the minimum price
  - One for the median price
  - One for the maximum price
- Now pivot the data to have a column per class for each quarter and whether the passenger had a flow card or not
- Union these flows back together
- What's this you see??? Economy is the most expensive seats and first class is the cheapest? When you go and check with your manager you realise the original data has been incorrectly classified so you need to the names of these columns.
  - Change the name of the following columns:
    - Economy to First
    - First Class to Economy
    - Business Class to Premium
    - Premium Economy to Business
Output the data
*The data source is in the **PD 2024 Week2** folder*
  <br>
  <br>



**SQL Solution** (*In Snowflake*)  


    WITH 

        CTE1 AS (SELECT * FROM PD2024_W2_FLOWCARD
                 UNION
                 SELECT * FROM PD2024_WK2_NONFLOWCARD),
        
        FLOW1 AS (SELECT QUARTER(TO_DATE(DATE, 'DD/MM/YYYY')) AS QUARTER,
                        CASE WHEN "Flow Card?" = TRUE THEN 'Yes' ELSE 'No' END AS FLOW_CARD,
                        CLASS,
                        MEDIAN(PRICE) AS MEDIAN_PRICE,
                        'Median' AS AGG_UNIT
                FROM CTE1
                GROUP BY QUARTER, FLOW_CARD, CLASS, AGG_UNIT),
                
        FLOW2 AS (SELECT QUARTER(TO_DATE(DATE, 'DD/MM/YYYY')) AS QUARTER,
                        CASE WHEN "Flow Card?" = TRUE THEN 'Yes' ELSE 'No' END AS FLOW_CARD,
                        CLASS,
                        MIN(PRICE) AS MINIMUM_PRICE,
                        'Minimum' AS AGG_UNIT
                FROM CTE1
                GROUP BY QUARTER, FLOW_CARD, CLASS, AGG_UNIT),
        
        FLOW3 AS (SELECT QUARTER(TO_DATE(DATE, 'DD/MM/YYYY')) AS QUARTER,
                        CASE WHEN "Flow Card?" = TRUE THEN 'Yes' ELSE 'No' END AS FLOW_CARD,
                        CLASS,
                        MAX(PRICE) AS MAXIMUM_PRICE,
                        'Maximum' AS AGG_UNIT
                FROM CTE1
                GROUP BY QUARTER, FLOW_CARD, CLASS, AGG_UNIT),        
                
        PIV_FLOW1 AS (SELECT * 
                      FROM FLOW1
                      PIVOT(SUM(MEDIAN_PRICE) FOR CLASS IN ('Premium Economy', 'Economy', 'First Class', 'Business Class'))
                      ),
        
        PIV_FLOW2 AS (SELECT * 
                      FROM FLOW2
                      PIVOT(SUM(MINIMUM_PRICE) FOR CLASS IN ('Premium Economy', 'Economy', 'First Class', 'Business Class'))
                      ),
        
        PIV_FLOW3 AS (SELECT * 
                      FROM FLOW3
                      PIVOT(SUM(MAXIMUM_PRICE) FOR CLASS IN ('Premium Economy', 'Economy', 'First Class', 'Business Class'))
                      ),
        
        FLOWS_UNION AS (SELECT * FROM PIV_FLOW1
                        UNION
                        SELECT * FROM PIV_FLOW2
                        UNION
                        SELECT * FROM PIV_FLOW3
                        )
       
    SELECT FLOW_CARD ,
        QUARTER,
        ROUND("'First Class'",1) AS Economy,
        ROUND("'Business Class'", 1) AS Premium,
        ROUND("'Premium Economy'", 1) AS Business,
        ROUND("'Economy'", 1) AS First,
        AGG_UNIT AS Aggregated_Unit
    FROM FLOWS_UNION
            ;

        
**Ouput** 
|FLOW_CARD|QUARTER|ECONOMY|PREMIUM|BUSINESS|FIRST |AGGREGATED_UNIT|
|---------|-------|-------|-------|--------|------|---------------|
|No       |2      |445.0  |554.0  |1205.0  |2325.0|Median         |
|Yes      |1      |447.5  |523.0  |1160.0  |2325.0|Median         |
|No       |4      |428.0  |556.0  |1063.0  |2202.5|Median         |
|Yes      |2      |459.0  |518.0  |1071.5  |2290.0|Median         |
|Yes      |4      |424.0  |522.5  |1109.0  |2212.5|Median         |
|No       |3      |487.0  |491.0  |1125.5  |2285.0|Median         |
|No       |1      |438.0  |575.0  |1075.0  |2340.0|Median         |
|Yes      |3      |457.0  |554.0  |1090.0  |2347.5|Median         |
|Yes      |3      |206.0  |241.0  |503.0   |1005.0|Minimum        |
|Yes      |2      |200.0  |240.0  |500.0   |1020.0|Minimum        |
|No       |3      |201.0  |240.0  |518.0   |1000.0|Minimum        |
|No       |4      |200.0  |240.0  |510.0   |1015.0|Minimum        |
|Yes      |4      |205.0  |250.0  |505.0   |1030.0|Minimum        |
|No       |2      |202.0  |240.0  |508.0   |1000.0|Minimum        |
|Yes      |3      |697.0  |840.0  |1750.0  |3495.0|Maximum        |
|Yes      |2      |696.0  |840.0  |1738.0  |3490.0|Maximum        |
|Yes      |1      |698.0  |840.0  |1738.0  |3500.0|Maximum        |
|No       |2      |694.0  |828.0  |1745.0  |3480.0|Maximum        |
|No       |4      |698.0  |835.0  |1730.0  |3465.0|Maximum        |
|Yes      |1      |201.0  |250.0  |503.0   |1020.0|Minimum        |
|No       |1      |204.0  |241.0  |515.0   |1030.0|Minimum        |
|Yes      |4      |697.0  |834.0  |1723.0  |3460.0|Maximum        |
|No       |1      |699.0  |834.0  |1703.0  |3455.0|Maximum        |
|No       |3      |691.0  |839.0  |1748.0  |3475.0|Maximum        |


<br>
<br>

