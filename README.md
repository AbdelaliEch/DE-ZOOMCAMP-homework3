# DE-ZOOMCAMP-homework3

## SETUP:  
Create an external table using the Green Taxi Trip Records Data for 2022.
```sql
CREATE OR REPLACE EXTERNAL TABLE `de_zoomcamp.green_tripdata_2022_external`
OPTIONS (
  format = 'PARQUET',
  uris = ['gs://kestra-project-448315-bucket/green_tripdata_2022-*.parquet']
);
```
Create a table in BQ using the Green Taxi Trip Records for 2022 (do not partition or cluster this table).
```sql
CREATE OR REPLACE TABLE `de_zoomcamp.green_tripdata_2022` AS
SELECT * FROM `de_zoomcamp.green_tripdata_2022_external`;
```

## Question 1:
What is count of records for the 2022 Green Taxi Data??
- 65,623,481
- 840,402
- 1,936,423
- 253,647  
```sql
SELECT COUNT(1) FROM `de_zoomcamp.green_tripdata_2022`;
```
**Answer:** 840,402

## Question 2:
Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables.</br> 
What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?

- 0 MB for the External Table and 6.41MB for the Materialized Table
- 18.82 MB for the External Table and 47.60 MB for the Materialized Table
- 0 MB for the External Table and 0MB for the Materialized Table
- 2.14 MB for the External Table and 0MB for the Materialized Table  
```sql
SELECT COUNT(DISTINCT PULocationID) FROM `de_zoomcamp.green_tripdata_2022_external`;
SELECT COUNT(DISTINCT PULocationID) FROM `de_zoomcamp.green_tripdata_2022`;
```
**Answer:** 0 MB for the External Table and 6.41MB for the Materialized Table

## Question 3:
How many records have a fare_amount of 0?
- 12,488
- 128,219
- 112
- 1,622  
```sql
SELECT COUNT(1) FROM `de_zoomcamp.green_tripdata_2022` WHERE fare_amount=0; 
```
**Answer:** 1,622

## Question 4:
What is the best strategy to make an optimized table in Big Query if your query will always order the results by PUlocationID and filter based on lpep_pickup_datetime? (Create a new table with this strategy)
- Cluster on lpep_pickup_datetime Partition by PUlocationID
- Partition by lpep_pickup_datetime  Cluster on PUlocationID
- Partition by lpep_pickup_datetime and Partition by PUlocationID
- Cluster on by lpep_pickup_datetime and Cluster on PUlocationID  
```sql
CREATE OR REPLACE TABLE `de_zoomcamp.green_tripdata_2022_partitioned_clustered`
PARTITION BY DATE(lpep_pickup_datetime)
CLUSTER BY PULocationID
AS
SELECT * FROM `de_zoomcamp.green_tripdata_2022`;
```
**Answer:** Partition by lpep_pickup_datetime Cluster on PUlocationID

## Question 5:
Write a query to retrieve the distinct PULocationID between lpep_pickup_datetime
06/01/2022 and 06/30/2022 (inclusive)</br>

Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table you created for question 4 and note the estimated bytes processed. What are these values? </br>

Choose the answer which most closely matches.</br> 

- 22.82 MB for non-partitioned table and 647.87 MB for the partitioned table
- 12.82 MB for non-partitioned table and 1.12 MB for the partitioned table
- 5.63 MB for non-partitioned table and 0 MB for the partitioned table
- 10.31 MB for non-partitioned table and 10.31 MB for the partitioned table  
```sql
SELECT DISTINCT(PULocationID) FROM `de_zoomcamp.green_tripdata_2022`
WHERE lpep_pickup_datetime>='2022-06-01 00:00:00' AND lpep_pickup_datetime<='2022-06-30 00:00:00';

SELECT DISTINCT(PULocationID) FROM `de_zoomcamp.green_tripdata_2022_partitioned_clustered`
WHERE lpep_pickup_datetime>='2022-06-01 00:00:00' AND lpep_pickup_datetime<='2022-06-30 00:00:00';
```
**Answer:** 12.82 MB for non-partitioned table and 1.12 MB for the partitioned table

## Question 6: 
Where is the data stored in the External Table you created?

- Big Query
- GCP Bucket
- Big Table
- Container Registry  

**Answer:** GCP Bucket

## Question 7:
It is best practice in Big Query to always cluster your data:
- True
- False  

**Answer:** False
