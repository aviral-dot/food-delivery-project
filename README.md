![image alt](https://github.com/aviral-dot/real-time-food-delivery-project/blob/main/aws_project_architecture.drawio.png?raw=true)


THE ABOVE IS THE ARCHITECTURE OF REAL TIME FODD DELIVERY DATA PIPELINE WHICH INVOLVES THE USE OF AWS KINESIS , AWS EMR , AWS REDSHIFT , AWS S3 BUCKET , AWS CODEBUILD AND MOCK DATA GENERATOR.

⚡️ Amazon Kinesis — Real-Time Data Streaming on AWS

Amazon Kinesis is a fully managed service from AWS (Amazon Web Services) designed to collect, process, and analyze real-time streaming data. It's useful when you need to handle large volumes of data as it arrives, especially in real time.

THE KINESIS USES KINESIS STREAMS IN THE ABOVE PROJECT TAHT TAKES IN REAL-TIME FOOD DELIVERY DATA WHICH HAS 
        'OrderID'
        'CustomerID' 
        'RestaurantID'
        'RiderID'
        'OrderDate'
        'DeliveryTime'
        'OrderValue'
        'DeliveryFee'
        'TipAmount'
        'OrderStatus'
AS THE IMPORTANT DATA IN IT.THE KINESIS STORES THE REAL TIME DATA IN THE KINESIS SHARDS.

Shard
A shard is a unit of capacity and storage in Kinesis.

Each shard:

Can ingest up to 1 MB/sec or 1000 records/sec.

Can emit up to 2 MB/sec to consumers.

Stores data for a default of 24 hours (can be increased to 7 days)

 Record
Each record in Kinesis contains:

Data blob: Your actual data (e.g., JSON, CSV, etc., up to 1 MB).

Partition key: Used to group data (and determine shard placement).

Sequence number: Unique ID per record for ordering.

Example Record:
json
Copy
Edit
{
  "partitionKey": "user-1234",
  "data": "base64-encoded-data",
  "sequenceNumber": "49590338271490256608559692538361571095921575989136588898"
}

![image alt](https://github.com/aviral-dot/real-time-food-delivery-project/blob/main/emr.png?raw=true)

✅ Amazon EMR (Elastic MapReduce)


Amazon EMR (Elastic MapReduce) is a big data processing service provided by AWS. It allows you to easily run large-scale distributed data processing jobs using frameworks like Apache Spark, Hadoop, Hive, Presto, and more — without managing infrastructure.

A PySpark script is a Python program that uses Apache Spark (via the PySpark library) to process and analyze big data in a distributed computing environment.

🔥 What is PySpark?
PySpark = Python + Apache Spark
It allows you to write Spark applications in Python, instead of Java or Scala.

Apache Spark is a fast, in-memory data processing engine used for big data workloads like:

ETL (Extract, Transform, Load)

Machine learning

Real-time stream processing

SQL queries

🧾 What Does a PySpark Script Look Like?
A PySpark script typically:

Initializes a Spark session

Reads data (CSV, JSON, Parquet, from S3, HDFS, etc.)

Transforms data (filter, map, join, group, aggregate, etc.)

Performs actions (show, save to file, write to DB, etc.)



AMAZON EMR USED IN THIS PROJECT TO CLEAN,TRANFORM THE DATA COMING FROM KINESIS WHICH IS THEN STORED IN AMZON REDSHIFT DATAWAREHOUSE TO BE USED FOR GENERATING INSIGHTS FROM THAT DATA. A PYSPARK STREAMING SCRIPT IS DEVELOPED WHICH IS USED IN THE EMR FOR CLEANING.TRANFOMRATION AND PROCESSING


