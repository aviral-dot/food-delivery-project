![image alt](https://github.com/aviral-dot/real-time-food-delivery-project/blob/main/aws_project_architecture.drawio.png?raw=true)


THE ABOVE IS THE ARCHITECTURE OF REAL TIME FODD DELIVERY DATA PIPELINE WHICH INVOLVES THE USE OF AWS KINESIS , AWS EMR , AWS REDSHIFT , AWS S3 BUCKET , AWS CODEBUILD AND MOCK DATA GENERATOR.

**⚡️ Amazon Kinesis — Real-Time Data Streaming on AWS**

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

**✅ Amazon EMR (Elastic MapReduce)**


Amazon EMR (Elastic MapReduce) is a big data processing service provided by AWS. It allows you to easily run large-scale distributed data processing jobs using frameworks like Apache Spark, Hadoop, Hive, Presto, and more — without managing infrastructure.

A PySpark script is a Python program that uses Apache Spark (via the PySpark library) to process and analyze big data in a distributed computing environment.

**🔥 What is PySpark?**

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



AMAZON EMR USED IN THIS PROJECT TO CLEAN,TRANFORM THE DATA COMING FROM KINESIS WHICH IS THEN STORED IN AMZON REDSHIFT DATAWAREHOUSE TO BE USED FOR GENERATING INSIGHTS FROM THAT DATA. 
A PYSPARK STREAMING SCRIPT IS DEVELOPED WHICH IS USED IN THE EMR FOR CLEANING.TRANFOMRATION AND PROCESSING

![image alt](https://github.com/aviral-dot/real-time-food-delivery-project/blob/main/emr2.png?raw=true)

THE ABOVE IMAGE SHOWS THAT AMAZON EMR IS FULL READY AND IN ACTIVE STATE ALONG WITH IT ON THE PRIMARY NODE IT OFFERS VARIOUS SERVICES WHICH WERE CHOSEN DURING THE MAKING OF AMAZON EMR.
THE FOLLOWINF SERVICES -
HDFS NODE
HUE 
LIVY
RESOURCE MANAGER
SPARK HISTORY SERVER

![image alt](https://github.com/aviral-dot/real-time-food-delivery-project/blob/main/Screenshot%202025-01-18%20195416.png?raw=true)

**📊 Amazon Redshift — AWS Data Warehouse Service**

Amazon Redshift is a fully managed cloud-based data warehouse provided by AWS. 
It allows you to run high-performance SQL analytics on large volumes of structured and semi-structured data (in terabytes or even petabytes).

AMAZON REDSHIFT IS USED IN THIS PROJECT TO STORE DATA THAT COMES FROM AAMAZON EMR TO STORE THEM IN DATA WAREHOUSE TO BE USED BY BUSINESSES TO GENERATE INSIGHTS FROM THE DATA .
THE DATA IS STORED IN REDSHIFT IN STAR SCHEMA WHICH HAS FACT AND DIMENSIONS TABLE.


![image alt](https://github.com/aviral-dot/real-time-food-delivery-project/blob/main/Screenshot%202025-01-18%20195427.png)

An Amazon Airflow environment refers to a managed workflow orchestration environment provided by Amazon MWAA (Managed Workflows for Apache Airflow). It allows you to run and scale Apache Airflow workflows on AWS without having to manage the underlying infrastructure.

**✅ What is Apache Airflow?**
Apache Airflow is an open-source platform used to programmatically author, schedule, and monitor workflows (DAGs). It is widely used in data engineering, ETL pipelines, ML workflows, etc.

**✅ What is Amazon MWAA?**
Amazon MWAA (Managed Workflows for Apache Airflow) is a fully managed service that makes it easy to run Apache Airflow on AWS.

APACHE AIRFLOW IS USED IN THIS PROJECT TO CREATE TWO DAGS AND ORCHESTRATE THEM :
1) **airflow_to_emr.py**

THIS DAG IS USED TO SPIN THE SPARK CLUSTER IN THE ACTIVE AMAZON EMR SERVICE SO THAT PROCESSING OF REAL TIME INCOMING DATA CAN BE DONE.
THIS CREATE A SPARK CLUSTER ALONG WITH THE FOLLOWING CONFIGURATION.
step_adder = EmrAddStepsOperator(
    task_id='add_step',
    job_flow_id='j-2FN0DTES2KKFF',
    aws_conn_id='aws_default',
    steps=[{
        'Name': 'Run PySpark Streaming Script',
        'ActionOnFailure': 'CONTINUE',
        'HadoopJarStep': {
            'Jar': 'command-runner.jar',
            'Args': [
                'spark-submit',
                '--deploy-mode',
                'cluster',
                '--num-executors', '3',
                '--executor-memory', '6G',
                '--executor-cores', '3',
                '--packages', packages_list,
                '--jars', jdbc_jar_s3_path,
                's3://food-important-files/pyspark_script/pyspark_streaming.py',
                '--redshift_user', redshift_user,
                '--redshift_password', redshift_password,
                '--aws_access_key', aws_access_key,
                '--aws_secret_key', aws_secret_key,
            ],
        },
    }],
    dag=dag,
)

**2)dim_load_dag.py:**

THIS DAG OF AIRFLOW IS USED TO LOAD THE DIMENSION TABLE OF dimCustomers , dimRestaurants , dimDeliveryRiders , WITH DATA AND factOrders TABLE WILL KEEP THE RECORD OF REAL TIME DATA ACCORDING TO THE STAR SCHEMA STRUCTURE.

**⭐ What is Star Schema?**
A Star Schema organizes data into fact and dimension tables:

Fact Table: Central table that stores quantitative data (metrics/measures).

Dimension Tables: Surrounding tables that store descriptive attributes (who, what, when, where, how).

The schema resembles a star shape, hence the name.

🧱 Structure:
lua
Copy
Edit
               +-------------+
               |  Dim_Date   |
               +-------------+
                     |
                     |
+------------+   +------------+   +------------+   +------------+
| Dim_Customer | | Dim_Product | | Dim_Region  | | Dim_SalesRep|
+------------+   +------------+   +------------+   +------------+
       \             |              |              /
        \            |              |             /
                  +----------------------+
                  |      Fact_Sales      |
                  +----------------------+
                  | date_key             |
                  | customer_key         |
                  | product_key          |
                  | region_key           |
                  | salesrep_key         |
                  | total_sales          |
                  | quantity_sold        |
                  +----------------------+
                  

![image alt](https://github.com/aviral-dot/real-time-food-delivery-project/blob/main/Screenshot%202025-01-18%20230754.png?raw=true)

THE ABOVE IMAGE SHOWS THE SPINNING UP OF BOTH THE DAG.

![image alt](https://github.com/aviral-dot/real-time-food-delivery-project/blob/main/Screenshot%202025-01-18%20230912.png?raw=true)

THE ABOVE IMAGE SHOWS THE GRAPH OF THE DAGS THAT ARE SPINNED UP.
In Apache Airflow, a Graph refers to the graphical representation of a DAG (Directed Acyclic Graph) — a workflow made up of tasks with dependencies. This is visualized in the Graph View inside the Airflow UI.

**🧭 What is a DAG?**
A DAG (Directed Acyclic Graph) is a collection of tasks (nodes) connected by dependencies (edges). The tasks are executed in a specific order defined by the graph.

In Airflow:

Each task is a node.

Dependencies are arrows between tasks.

![image alt](https://github.com/aviral-dot/real-time-food-delivery-project/blob/main/Screenshot%202025-01-18%20230831.png?raw=true)

THE ABOVE IMAGE SHOWS THE CODEBUILD RUNNING AND BUILDING THE TASK. WE HAVE BUILDSPE.YML FILE IN THE CODEBUILD

**🚀 What is Amazon CodeBuild?**

Amazon CodeBuild is a fully managed continuous integration (CI) service by AWS that compiles source code, runs tests, and produces build artifacts. It eliminates the need to provision, manage, and scale your own build servers.

BUIDSPEC.YML DIRECTS THE JOB TO BE DONE OF CI/CD SIDE.




