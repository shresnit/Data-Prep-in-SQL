**2023: Week 22 - Student Attendance vs Test Scores**
-------------------

May 31, 2023  
Challenge by: Tanbir Jalil  

This is our next challenge from one of the members of the 38th cohort of the Data School UK.

You have been hired to create a dataset for the Prep School that will allow them to analyse the correlation between attendance and test scores.  

PD Challenge: https://preppindata.blogspot.com/2023/05/2023-week-22-student-attendance-vs-test.html


Student Test Scores
<br>

|STUDENT_ID     |STUDENT_NAME|SUBJECT|TEST_SCORE|TEST_DATE|
|---------------|------------|-------|----------|---------|
|1001           |Alice_Smith |Math   |95.5      |1/15/2023|
|1020           |Tina_Lee    |Sciece |91.9      |1/21/2023|
|1019           |Sam_Kim     |Math   |89.6      |1/21/2023|
|1018           |Rachel_Tan  |Engish |85.8      |1/20/2023|
|1017           |Quinn_Wang  |Sciece |88.12     |1/20/2023|
|1016           |Paul_Jackson|Math   |87.45     |1/20/2023|
|1015           |Olivia_Kim  |Engish |91.7      |1/19/2023|
|1014           |Nate_Hall   |Sciece |94.25     |1/19/2023|
|1013           |Mary_Davis  |Math   |92.9      |1/19/2023|
|1012           |Liam_Cooper |Engish |87.1      |1/18/2023|
|1011           |Kate_Liu    |Sciece |89.75     |1/18/2023|
|1010           |Jack_Brown  |Math   |88.33     |1/18/2023|
|1009           |Irene_Wong  |Engish |83.2      |1/17/2023|
|1008           |Henry_Chen  |Sciece |92.75     |1/17/2023|
|1007           |Grace_Kim   |Math   |86.5      |1/17/2023|
|1006           |Frank_Lee   |Engish |76.8      |1/16/2023|
|1005           |Eve_Taylor  |Sciece |88        |1/16/2023|
|1004           |David_Wilson|Math   |91.25     |1/16/2023|
|1003           |Charlie_Brown|Sciece |78.75     |1/15/2023|
|1002           |Bob_Jones   |Engish |82        |1/15/2023|


<br>
Attendance Figures
<br>

|STUDENT_NAME   |ATTENDANCE_PERCENTAGE|
|---------------|---------------------|
|Alice_Smith    |0.75                 |
|Tina_Lee       |0.8                  |
|Sam_Kim        |1                    |
|Rachel_Tan     |0.6                  |
|Quinn_Wang     |0.7                  |
|Paul_Jackson   |0.9                  |
|Olivia_Kim     |1                    |
|Nate_Hall      |0.8                  |
|Mary_Davis     |0.75                 |
|Liam_Cooper    |0.6                  |
|Kate_Liu       |0.7                  |
|Jack_Brown     |0.85                 |
|Irene_Wong     |0.8                  |
|Henry_Chen     |0.9                  |
|Grace_Kim      |1                    |
|Frank_Lee      |0.6                  |
|Eve_Taylor     |0.7                  |
|David_Wilson   |0.8                  |
|Charlie_Brown  |0.95                 |
|Bob_Jones      |0.9                  |

<br>



**Requirements**
- Input the data
- First we must join the test score data set and the additional class attendance data set 
- Clean Data: there’s a few spelling mistakes in the data set, use the data tool of your choice to correct these mistakes before we proceed. These mistakes are in the “Subject”  Column
- Use a calculated field to clean up the test score column, so each number is a rounded to the nearest whole number
- Split the student name column so we can see the first name and surname in two separate columns, named appropriately
- If any student has an attendance percentage less than 70%, flag it as "Low Attendance" in a new column "attendance flag". Conversely, if above 90%, flag as “high attendance”. And anything else as “Medium”.  
- Output the data
*The data source is in the **PD2023Week22** folder*
  <br>
  <br>



**SQL Solution** (*In Snowflake*)  

    SELECT  CASE
        WHEN ATTENDANCE_PERCENTAGE < .7 THEN 'Low Attendance'
        WHEN ATTENDANCE_PERCENTAGE > .9 THEN 'High Attendance'
        ELSE 'Medium'
        END AS ATTENDANCE_FLAG,

        SPLIT_PART(A.STUDENT_NAME, '_', 1) AS FIRSTNAME,
        
        SPLIT_PART(A.STUDENT_NAME, '_', 2) AS SURNAME,
        
        A.ATTENDANCE_PERCENTAGE,
        
        B.STUDENT_ID,

        CASE
        WHEN B.SUBJECT LIKE 'M%' THEN 'Math'
        WHEN B.SUBJECT LIKE 'E%' THEN 'English'
        WHEN B.SUBJECT LIKE 'S%' THEN 'Science'
        END AS SUBJECT,

        B.TEST_SCORE,

        ROUND(B.TEST_SCORE) AS TEST_SCORE_INTEGER
                
    FROM PD2023_WK22_ATTENDANCE_FIGURES AS A
    INNER JOIN PD2023_WK22_STUDENT_TEST_SCORES AS B
        ON A.STUDENT_NAME = B.STUDENT_NAME
    ;
            
**Output** 
|ATTENDANCE_FLAG|FIRSTNAME|SURNAME|ATTENDANCE_PERCENTAGE|STUDENT_ID|SUBJECT|TEST_SCORE|TEST_SCORE_INTEGER|
|---------------|---------|-------|---------------------|----------|-------|----------|------------------|
|Medium         |Alice    |Smith  |0.75                 |1001      |Math   |95.5      |96                |
|Medium         |Tina     |Lee    |0.8                  |1020      |Science|91.9      |92                |
|High Attendance|Sam      |Kim    |1                    |1019      |Math   |89.6      |90                |
|Low Attendance |Rachel   |Tan    |0.6                  |1018      |English|85.8      |86                |
|Medium         |Quinn    |Wang   |0.7                  |1017      |Science|88.12     |88                |
|Medium         |Paul     |Jackson|0.9                  |1016      |Math   |87.45     |87                |
|High Attendance|Olivia   |Kim    |1                    |1015      |English|91.7      |92                |
|Medium         |Nate     |Hall   |0.8                  |1014      |Science|94.25     |94                |
|Medium         |Mary     |Davis  |0.75                 |1013      |Math   |92.9      |93                |
|Low Attendance |Liam     |Cooper |0.6                  |1012      |English|87.1      |87                |
|Medium         |Kate     |Liu    |0.7                  |1011      |Science|89.75     |90                |
|Medium         |Jack     |Brown  |0.85                 |1010      |Math   |88.33     |88                |
|Medium         |Irene    |Wong   |0.8                  |1009      |English|83.2      |83                |
|Medium         |Henry    |Chen   |0.9                  |1008      |Science|92.75     |93                |
|High Attendance|Grace    |Kim    |1                    |1007      |Math   |86.5      |87                |
|Low Attendance |Frank    |Lee    |0.6                  |1006      |English|76.8      |77                |
|Medium         |Eve      |Taylor |0.7                  |1005      |Science|88        |88                |
|Medium         |David    |Wilson |0.8                  |1004      |Math   |91.25     |91                |
|High Attendance|Charlie  |Brown  |0.95                 |1003      |Science|78.75     |79                |
|Medium         |Bob      |Jones  |0.9                  |1002      |English|82        |82                |


<br>
<br>


