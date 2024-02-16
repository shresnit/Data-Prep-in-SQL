**2024: Week 4 - Unpopular Seats**
-------------------

January 24, 2024  
Created by: Carl Allchin  
<br>
Last week you needed to use a Join technique to pair the flight data with the sales targets. This week you'll be using Joins again but this time in a different way.   

When using Joins, there are two things you need to set up:
- Join Condition - what logic will join similar rows of data together from each data set
- Join Type - determines what data you will bring back based on the Join Condition
<br>
This challenge will test using join types to return the data you require for the output.
<br>
<br>


**Requirements**
- Input the data
- Union the Flow Card and Non-Flow card data sets together
- Create a data field to show whether the seat was booked by someone with the Flow Card or not
  - Call this field 'Flow Card?'
- Aggregate the Seat Bookings to count how many bookings there are for:
  - Each Seat
  - In each Row
  - In each Class
  - For Flow and Non-Flow Card holders
- Join on the Seating Plan data to ensure you have a data set for every seat on the plane, even if it hasn't been book
  - Only return the records for the seats that haven't been booked
- Output the data set showing what seat, rows and class have NOT been booked
*The data source is in the **PD2024 Week4** folder*
  <br>
  <br>



**SQL Solution** (*In Snowflake*)  


    WITH
      CTE1 AS (SELECT *,
                  'Yes' AS FLOW_CARD
               FROM PD2024_WK4_INPUT_FLOW_CARD
               
               UNION
  
               SELECT *,
                  'No' AS FLOW_CARD
               FROM PD2024_WK4_INPUT_NON_FLOW_CARD
               ),
  
      CTE2 AS (SELECT CLASS,
                  ROW_,
                  SEAT,
                  FLOW_CARD,
                  COUNT(*) AS BOOKINGS
              FROM CTE1
              GROUP BY CLASS, ROW_, SEAT, FLOW_CARD)

    SELECT S.CLASS,
        S.ROW_,
        S.SEAT
    FROM CTE2
    RIGHT JOIN PD2024_WK4_INPUT_SEAT_PLAN AS S
        ON CTE2.SEAT = S.SEAT AND CTE2.CLASS = S.CLASS AND CTE2.ROW_ = S.ROW_
    WHERE CTE2.BOOKINGS IS NULL
    ;
        
**Output** 
|CLASS|ROW_|SEAT|
|-----|----|----|
|E    |32  |6   |
|E    |36  |5   |
|E    |41  |5   |
|E    |37  |6   |
|E    |40  |6   |
|E    |40  |5   |
|E    |28  |5   |

<br>
<br>

