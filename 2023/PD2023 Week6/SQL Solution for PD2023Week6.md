**2023: Week 6 - DSB Customer Ratings**
-------------------

February 08, 2023  
Challenge by: Jenny Martin

For the second intermediate challenge, Data Source Bank are interested in surveying their customers. They're trying to work out whether users prefer to use the Online Interface for their banking requirements, or whether they prefer the Mobile App. Customers can be quite fickle and so it's probably best to take some of their ratings with a pinch of salt! We'll use an aggregated view to hopefully cut through the noise.
  
The results of a survey asking customers to rate, on a scale of 1-5, different areas for the Mobile App and the Online Interface.
![image](https://github.com/shresnit/SQL-Projects/assets/100710335/e5b20e33-87ad-4dd5-8564-73ffc0031297)
<br>

**Requirements**
- Input the data
- Reshape the data so we have 5 rows for each customer, with responses for the Mobile App and Online Interface being in separate fields on the same row
- Clean the question categories so they don't have the platform in from of them
  - e.g. Mobile App - Ease of Use should be simply Ease of Use
- Exclude the Overall Ratings, these were incorrectly calculated by the system
- Calculate the Average Ratings for each platform for each customer 
- Calculate the difference in Average Rating between Mobile App and Online Interface for each customer
- Catergorise customers as being:
  - Mobile App Superfans if the difference is greater than or equal to 2 in the Mobile App's favour
  - Mobile App Fans if difference >= 1
  - Online Interface Fan
  - Online Interface Superfan
  - Neutral if difference is between 0 and 1
- Calculate the Percent of Total customers in each category, rounded to 1 decimal place
- Output the data  
*The data source is in the **PD2023Week6** folder*
  <br>
  <br>

**SQL Solution** (*In Snowflake*)  


    WITH 
          CTE1 AS 
      
                  (SELECT *
                          
                  FROM (
                          SELECT CUSTOMER_ID,
                                  split_part(INTERFACE, '___', 1) AS INTERFACE,
                                  split_part(INTERFACE, '___', 2) AS RESPONSES,
                                  RATINGS
                          FROM PD2023_WK06_DSB_CUSTOMER_SURVEY
                          UNPIVOT ( RATINGS
                                      FOR INTERFACE IN (MOBILE_APP___EASE_OF_USE, 
                                                          MOBILE_APP___EASE_OF_ACCESS, 
                                                          MOBILE_APP___NAVIGATION, 
                                                          MOBILE_APP___LIKELIHOOD_TO_RECOMMEND, 
                                                          MOBILE_APP___OVERALL_RATING,
                                                          ONLINE_INTERFACE___EASE_OF_USE, 
                                                          ONLINE_INTERFACE___EASE_OF_ACCESS, 
                                                          ONLINE_INTERFACE___NAVIGATION, 
                                                          ONLINE_INTERFACE___LIKELIHOOD_TO_RECOMMEND, 
                                                          ONLINE_INTERFACE___OVERALL_RATING 
                                                          )
                                  )
                          )
                  
                  PIVOT ( SUM(RATINGS) FOR INTERFACE IN ('MOBILE_APP', 'ONLINE_INTERFACE')) AS P (CUSTOMER_ID, RESPONSES, MOBILE_APP, ONLINE_INTERFACE)
                  WHERE RESPONSES NOT LIKE '%OVERALL%'
                  ),
            
          CTE2 AS
      
                  (SELECT CUSTOMER_ID,
                          CASE
                              WHEN (AVG(MOBILE_APP) - AVG(ONLINE_INTERFACE)) >= 2 THEN 'Mobile App Superfan'
                              WHEN (AVG(MOBILE_APP) - AVG(ONLINE_INTERFACE)) >= 1 THEN 'Mobile App Fan'
                              WHEN (AVG(MOBILE_APP) - AVG(ONLINE_INTERFACE)) <= -2 THEN 'Online Interface Superfan'
                              WHEN (AVG(MOBILE_APP) - AVG(ONLINE_INTERFACE)) <= -1 THEN 'Online Interface Fan'
                              ELSE 'Neutral'
                          END AS "PREFERENCE",
                          'Total'AS TOTAL
                  FROM CTE1
                  GROUP BY CUSTOMER_ID
                  )

    SELECT PREFERENCE,
            ROUND((COUNT(*) / SUM(COUNT(*)) OVER (PARTITION BY TOTAL ) * 100), 1) AS "%_OF_TOTAL"
    FROM CTE2
    GROUP BY PREFERENCE, TOTAL
    ;
        
**Ouput** 
|PREFERENCE               |%_OF_TOTAL|
|-------------------------|----------|
|Neutral                  |63.7      |
|Online Interface Fan     |14.7      |
|Mobile App Fan           |16.4      |
|Mobile App Superfan      |2.6       |
|Online Interface Superfan|2.6       |


<br>
<br>


