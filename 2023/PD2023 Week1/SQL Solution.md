**2023: Week 1 The Data Source Bank**
-------------------

January 04, 2023  
Challenge by: Carl Allchin

The subject for January will be our new (fake) bank -- The Data Source Bank (DSB). This week we have had a report with a number of transactions that have not just our transactions but other banks' too. Can you help clean up the data?


**Requirements**
- Input the data (help)
- Split the Transaction Code to extract the letters at the start of the transaction code. These identify the bank who processes the transaction (help)
  - Rename the new field with the Bank code 'Bank'. 
- Rename the values in the Online or In-person field, Online of the 1 values and In-Person for the 2 values. 
- Change the date to be the day of the week (help)
- Different levels of detail are required in the outputs. You will need to sum up the values of the transactions in three ways (help):
  - 1. Total Values of Transactions by each bank
  - 2. Total Values by Bank, Day of the Week and Type of Transaction (Online or In-Person)
  - 3. Total Values by Bank and Customer Code
- Output each data file (help)
*The data source is in the PD2023Week1 folder*
  <br>
  <br>



**Question 1**  
Output Total Values of Transactions by each bank  

<br>  

**SQL Solution** (*In Snowflake*)  


    SELECT split_part(TRANSACTION_CODE, '-', 1) AS BANK,
        SUM(VALUE) AS VALUE
    FROM PD2023_WK01
    GROUP BY BANK
    ;
        
**Ouput 1** 
|BANK|VALUE                 |
|----|----------------------|
|DTB |618238                |
|DS  |653940                |
|DSB |530489                |

<br>
<br>

**Question 2**  
Output Total Values by Bank, Day of the Week and Type of Transaction  

<br>

**SQL Solution** (*In Snowflake*)  


    SELECT split_part(TRANSACTION_CODE, '-', 1) AS BANK,
        
        CASE
            WHEN ONLINE_OR_IN_PERSON = 1 THEN 'Online'
            WHEN ONLINE_OR_IN_PERSON = 2 THEN 'In-Person'
        END AS ONLINE_OR_IN_PERSON,
    
        DAYNAME(TO_DATE(TRANSACTION_DATE, 'DD/MM/YYYY HH:MI:SS')) AS TRANSACTION_DATE_DAY,
    
        SUM(VALUE) AS VALUE
    FROM PD2023_WK01
    GROUP BY BANK, ONLINE_OR_IN_PERSON, TRANSACTION_DATE_DAY
    ;
        
**Ouput 2** 
|BANK|ONLINE_OR_IN_PERSON   |TRANSACTION_DATE_DAY|VALUE|
|----|----------------------|--------------------|-----|
|DTB |In-Person             |Mon                 |21561|
|DS  |Online                |Mon                 |33563|
|DS  |Online                |Wed                 |59104|
|DS  |In-Person             |Thu                 |75582|
|DS  |In-Person             |Sun                 |51301|
|DTB |Online                |Sun                 |49087|
|DTB |Online                |Thu                 |53756|
|DS  |In-Person             |Fri                 |58599|
|DTB |Online                |Mon                 |38688|
|DSB |Online                |Wed                 |47372|
|DTB |In-Person             |Sun                 |29468|
|DSB |In-Person             |Wed                 |36069|
|DSB |Online                |Fri                 |45647|
|DS  |In-Person             |Wed                 |63686|
|DTB |Online                |Wed                 |35313|
|DTB |Online                |Tue                 |68959|
|DSB |Online                |Tue                 |32257|
|DSB |In-Person             |Thu                 |37567|
|DS  |Online                |Sun                 |21761|
|DSB |Online                |Mon                 |31692|
|DS  |Online                |Sat                 |71357|
|DTB |In-Person             |Sat                 |66334|
|DS  |In-Person             |Mon                 |42806|
|DSB |Online                |Thu                 |33463|
|DTB |Online                |Sat                 |21392|
|DSB |In-Person             |Fri                 |9402 |
|DS  |In-Person             |Sat                 |34867|
|DTB |In-Person             |Tue                 |54029|
|DS  |Online                |Fri                 |58731|
|DTB |In-Person             |Fri                 |41861|
|DTB |Online                |Fri                 |27366|
|DS  |Online                |Thu                 |13337|
|DSB |In-Person             |Mon                 |43546|
|DSB |In-Person             |Sat                 |72679|
|DSB |In-Person             |Tue                 |28604|
|DTB |In-Person             |Thu                 |57605|
|DS  |In-Person             |Tue                 |32607|
|DSB |Online                |Sun                 |13086|
|DSB |Online                |Sat                 |61350|
|DS  |Online                |Tue                 |36639|
|DTB |In-Person             |Wed                 |52819|
|DSB |In-Person             |Sun                 |37755|



<br>
<br>

**Question 3**  
Output Total Values by Bank and Customer Code  

<br>

**SQL Solution** (*In Snowflake*) 


    SELECT split_part(TRANSACTION_CODE, '-', 1) AS BANK,
        CUSTOMER_CODE,
        SUM(VALUE) AS VALUE
    FROM PD2023_WK01
    GROUP BY BANK, CUSTOMER_CODE
    ;
        
**Output 3** 
|BANK|CUSTOMER_CODE         |VALUE|
|----|----------------------|-----|
|DTB |100001                |60675|
|DS  |100009                |56581|
|DS  |100002                |69803|
|DS  |100000                |57909|
|DSB |100008                |47121|
|DS  |100001                |53063|
|DTB |100010                |71396|
|DTB |100009                |52926|
|DSB |100002                |27936|
|DS  |100005                |39668|
|DTB |100004                |44435|
|DS  |100008                |56400|
|DTB |100007                |29308|
|DSB |100010                |92654|
|DSB |100005                |56396|
|DTB |100008                |69352|
|DSB |100009                |51749|
|DTB |100003                |84574|
|DTB |100002                |48616|
|DTB |100000                |77252|
|DS  |100007                |76190|
|DSB |100006                |32333|
|DS  |100006                |77636|
|DTB |100005                |37795|
|DSB |100003                |58154|
|DTB |100006                |41909|
|DS  |100004                |63315|
|DS  |100003                |25482|
|DSB |100001                |67856|
|DSB |100004                |39003|
|DS  |100010                |77893|
|DSB |100000                |27585|
|DSB |100007                |29702|


<br>
<br>
