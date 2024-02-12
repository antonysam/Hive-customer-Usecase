# Hive-customer-Usecase

## Business Team wanted only id,age,prof in a pipe del format in a hdfs location of hdfs:///user/hduser/extdata of only 'PILOT' (4000001|55|PILOT) from the raw data provided by some source system placed in a location of /home/hduser/hive/data/custs (4000001,Kristina,Chung,55,Pilot)
```
1. create a database called retail_raw.
2. create a table namely cust_raw understanding the data /home/hduser/hive/data/custs in the retail_raw database.
3. create another database called retail_curated.
4. create a table namely cust_curated to generate the output in this location hdfs:///user/hduser/extdata like this data 4000001|55|PILOT using pipe delimiter.
5. Export the data using sqoop from the above /user/hduser/extdata location to the mysql table called cust_report.
6. Look at the datalake+lakehouse architecture given in the slide 11
```
