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
## 1. create a database called retail_raw.
```
start hadoop daemons
$ start-all.sh
start hive
$ hive

hive> create database retail_raw;
hive> use retail_raw;
```

## 2. create a table namely cust_raw understanding the data /home/hduser/hive/data/custs in the retail_raw database.
```
create a temporary managed table inorder to freeup the storage once the hive session over

hive> create temporary table cust_raw (id int,age int,profile string)
> row format delimited by '|'
> stored as textfile;

loading the data to hive will change the delimiter of the raw file as per the client requirement i.e '|'
$ cat /home/hduser/hive/data/custs| tr -s ',' '|' > /home/hduser/hive/custs_transf

loading the data into hive from linux landing pad
hive> load data local inpath '/home/hduser/hive/data/custs_transf' into table cust_raw;

```
## 3. create another database called retail_curated.
```
hive> create database retail_curated;
hive> use retail_curated;
```
## 4. create a table namely cust_curated to generate the output in this location hdfs:///user/hduser/extdata like this data 4000001|55|PILOT using pipe delimiter.
```
create a temporary manage table cust_curated1 for converting profile to upper case
hive> create temporary table cust_curated1 as select id,age,upper(profile) from retail_raw.cust_raw;

Create a external table to store the curated output
hive> create external table cust_curated (id,age,profile)
> row format delimited fields terminated by '|'
> stored as textfile
> location hdfs:///user/hduser/extdata;

hive> insert into cust_curated1 select * from cust_curated where profile='PILOT';
```
## 5. Export the data using sqoop from the above /user/hduser/extdata location to the mysql table called cust_report.
```
Open an another terminal open mysql
mysql -u root -p
mysql> create database cust_reports;
mysql> create table cust_report(id int,age int,profile varchar(35));

Run sqoop in another terminal
$ sqoop export --connect jdbc:mysql://localhost/cust_reports --table cust_report --username root --password Root123$ -m 1 --driver com.mysql.cj.jdbc.Driver  --export-dir hdfs://localhost:54310/user/hduser/extdata/ --table cust_curated_temp --fields-terminated-by '|' --lines-terminated-by '/n' --driver com.mysql.cj.jdbc.Driver;


> select * from cust_reports;

```

 

