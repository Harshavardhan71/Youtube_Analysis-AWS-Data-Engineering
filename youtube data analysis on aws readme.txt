project:  youtube data analysis on aws
source code : kaggle

images details : name of the image files and its significance

1.project architecture
2. s3 buckets creation

3.aws cli commmand to upload raw data files
4.raw csv data 
	4.1raw jason data 
5.developed code
	5.1developed code
	5.2developed code
	5.3developed code and created a jar file

6.crating a lambda function 
7.creating IAM Roles
8.boto code for Lambda
9.upload a file to s3 to trigger a lambda function 
10cloudwatch to monitor the transaction
		10.1cloudwatchlog events
11.cluster created
	11.1cluster created
	11.2cluster created
	11.3cluster created
12.file created in s3 bronze csv
	12.1file created in s3 bronze json

13.file created in s3 silver joined	
14.glue crawler created 

15.jar file upload for trigger


project actions:
1. 5 buckets are created :
	dataengineeringprojects-sourcefiles
	dep-onyoutube-rawprocessed-bronze
	dep-onyoutube-rawprocessed-silver
	dep-onyoutube-rawprocessed-gold
	dep-lambda-triggers
2.using cli commands source data is uploaded to "	dataengineeringprojects-sourcefiles" bucket
#explain how

3.2 types of data is stored on that bucket
	csv :  raw_statistics
	json:	raw_statistics_refernce_data

4 developed 3 codes: by scala spark
	code1 to :process raw csv files 
	code2 to :process raw json files 
	code3 to :join the processed csv files and json files 
	the processing includes cleaning the files and convert them to parquet and patitioned
	the code 1 and code 2 are stored in bronze bucket.
	the code 3 finally stored in silver bucket.
	these 3 codes are converted to jar file		by maven build
	
5 lambda function is created for lambda trigger functionality:
		neccessary iam roles are created 
		and boto code is created : 
			This Python code is an AWS Lambda function that listens for S3 events (file uploads) and triggers the creation of an EMR cluster to run Spark jobs. Here's a breakdown:

1. Extracting S3 Event Data
When a file is uploaded to an S3 bucket, an event is triggered.
The function extracts:
The bucket name (bucket_name).
The object key (file path) (object_key).
2. Checking If the File Is in the 'Trigger' Folder
If the uploaded file is in the "trigger" folder, the function proceeds.
Otherwise, it logs the event and takes no action.
3. Checking for Existing EMR Clusters
It lists active EMR clusters (STARTING, BOOTSTRAPPING, RUNNING, WAITING).
If a cluster named "youtube_cluster" already exists, it skips cluster creation.
4. Creating an EMR Cluster
If no existing cluster is found, the function creates a new EMR cluster with:
EMR Version: 6.4.0
Master Node: 1 x m5.xlarge
Core Nodes: 2 x m5.xlarge
Log Storage: s3://{bucket_name}/logs/
IAM Roles: EMR_DefaultRole and EMR_EC2_DefaultRole
Installed Applications: Spark, Zeppelin
Scaling Behavior: The cluster terminates after task completion.
5. Submitting Spark Jobs
Once the EMR cluster is started, it submits three Spark jobs:

Processing Raw CSV Data

Runs the Spark job youtube_aws_maven.rawcsvObj from s3://{bucket_name}/jar_files/youtube_aws_maven.jar.
Processing Raw JSON Data

Runs the Spark job youtube_aws_maven.rawjsonObj.
Joining Cleaned Data

Runs the Spark job youtube_aws_maven.cleanedObj.
6. Handling Errors
If an error occurs during cluster creation, the function logs the error and returns a 500 HTTP status.
Summary
Step	Action
1	Extracts the S3 bucket name and file path from the event.
2	Checks if the file is in the "trigger" folder.
3	Checks if an EMR cluster (youtube_cluster) already exists.
4	If no cluster exists, it creates a new EMR cluster.
5	Submits 3 Spark jobs for processing raw CSV, JSON, and cleaned data.
6	Handles errors and logs the process.
This function automates data processing workflows using AWS Lambda, S3, and EMR. 🚀
		
		
		
	when the file is uploaded to the lambda bucket it should read the jar file 
	and complete the whole process 
	
	
the whole process is monitored on cloudwatch and its cloud watchlogs events
	which include cluster creation and perform the spark operation with neccesary config

after tghe files are written to bronze and silver buckets.
	the data from silver is ingesteded to glue crawler  aws athena to perform neccesary queries.

and further analysis by powerbi

	
	
	

************************************************************************************************************************
************************************************************************************************************************
************************************************************************************************************************
readme 1

# YouTube Data Analysis on AWS

## Overview
This project involves processing YouTube data using AWS services such as S3, AWS Lambda, EMR, Glue, and Athena. The pipeline ingests raw data from S3, processes it using Spark on EMR, stores processed data in different S3 buckets, and enables querying through Athena and visualization in Power BI.

## Project Architecture
The architecture consists of the following components:
1. **S3 Buckets:** Storage for raw, processed, and transformed data.
2. **AWS CLI:** Uploading raw data to S3.
3. **AWS Lambda:** Triggers EMR job execution when a new file is uploaded.
4. **EMR Cluster:** Runs Spark jobs to clean and process data.
5. **Glue Crawler:** Organizes and catalogs the processed data.
6. **Athena:** Queries structured data for analysis.
7. **Power BI:** Visualizes insights from processed data.

---

## Steps to Implement
### 1. S3 Buckets Creation
The following buckets are created:
- **dataengineeringprojects-sourcefiles**: Stores raw CSV and JSON files.
- **dep-onyoutube-rawprocessed-bronze**: Stores cleaned raw data in parquet format.
- **dep-onyoutube-rawprocessed-silver**: Stores transformed and joined data.
- **dep-onyoutube-rawprocessed-gold**: Stores final refined datasets for analysis.
- **dep-lambda-triggers**: Used for Lambda function triggers.

### 2. Uploading Data to S3 Using AWS CLI
To upload raw data files to S3, use the following commands:
```bash
aws s3 cp raw_statistics.csv s3://dataengineeringprojects-sourcefiles/
aws s3 cp raw_statistics_reference_data.json s3://dataengineeringprojects-sourcefiles/
```

### 3. Data Processing Workflow
#### 3.1. Data Storage
The source bucket contains two types of raw data:
- **CSV Files:** `raw_statistics.csv`
- **JSON Files:** `raw_statistics_reference_data.json`

#### 3.2. Developed Spark Code
Three Scala Spark codes are developed to process the data:
- **Code 1:** Processes raw CSV files, cleans them, converts them to Parquet, and partitions them before storing in the bronze bucket.
- **Code 2:** Processes raw JSON files similarly and stores the results in the bronze bucket.
- **Code 3:** Joins the processed CSV and JSON data, refines it, and stores it in the silver bucket.
- All three codes are compiled into a JAR file using Maven for deployment.

### 4. Lambda Function for EMR Trigger
A Lambda function is created to listen for S3 events and trigger an EMR cluster when a file is uploaded to the trigger bucket.

#### 4.1. IAM Roles
Necessary IAM roles are created to allow Lambda to trigger EMR clusters and access S3 data.

#### 4.2. Boto3 Code for AWS Lambda
The Lambda function performs the following steps:
1. Extracts bucket name and file path from the S3 event.
2. Verifies if the uploaded file is in the "trigger" folder.
3. Checks if an EMR cluster named `youtube_cluster` is already running.
4. If no cluster is found, creates a new EMR cluster with the following configuration:
   - EMR Version: 6.4.0
   - Master Node: `m5.xlarge`
   - Core Nodes: `2 x m5.xlarge`
   - Log Storage: `s3://{bucket_name}/logs/`
   - Installed Applications: Spark, Zeppelin
   - Cluster auto-terminates after completion.
5. Submits three Spark jobs for:
   - Processing raw CSV files (`youtube_aws_maven.rawcsvObj`)
   - Processing raw JSON files (`youtube_aws_maven.rawjsonObj`)
   - Joining cleaned CSV and JSON data (`youtube_aws_maven.cleanedObj`)
6. Logs errors and handles exceptions.

### 5. Upload a File to S3 to Trigger Lambda
Uploading a file to the trigger bucket initiates the Lambda function.
```bash
aws s3 cp trigger_file.txt s3://dep-lambda-triggers/
```

### 6. Monitoring with CloudWatch
AWS CloudWatch monitors the entire process, including:
- **CloudWatch Logs:** Records EMR cluster activities and Lambda function executions.
- **CloudWatch Log Events:** Tracks job success and failure.

### 7. EMR Cluster Creation
The EMR cluster is created dynamically when the Lambda function is triggered. Logs of its creation can be viewed in CloudWatch.

### 8. Processed Data Storage
- **Bronze Layer:** Contains cleaned raw CSV and JSON data.
- **Silver Layer:** Stores joined and transformed data.

### 9. Glue Crawler and Athena
- A Glue Crawler is set up to scan the silver bucket and catalog the data.
- AWS Athena is used to run queries on the structured data for analysis.

### 10. Visualization with Power BI
Once data is processed and structured in Athena, it can be visualized using Power BI for insights.

---

## Conclusion
This project successfully builds an automated YouTube data analysis pipeline using AWS services. The integration of Lambda, S3, EMR, Glue, and Athena ensures seamless data processing and analysis, making it an efficient cloud-based solution for handling large-scale YouTube data.














************************************************************************************************************************
************************************************************************************************************************
************************************************************************************************************************
readme 2

# YouTube Data Analysis on AWS

## Project Overview
This project focuses on analyzing YouTube data using AWS cloud services. The primary objective is to process raw data (CSV and JSON) using Apache Spark on AWS EMR, automate the workflow with AWS Lambda, and monitor the entire pipeline using AWS CloudWatch. The final processed data is stored in AWS Glue Data Catalog and queried using AWS Athena for further analysis in Power BI.

---

## Project Architecture
The architecture consists of multiple AWS components that work together to ingest, process, store, and visualize YouTube data. Below is a high-level workflow:

1. **Data Ingestion:** Raw YouTube statistics data (CSV & JSON) is uploaded to Amazon S3.
2. **Trigger Mechanism:** AWS Lambda detects new uploads and initiates an AWS EMR cluster.
3. **Processing Layer:** Apache Spark on EMR processes and cleans the data.
4. **Storage & Querying:** Processed data is stored in S3, registered in AWS Glue Data Catalog, and queried using AWS Athena.
5. **Visualization:** Power BI connects to Athena to generate insights.
6. **Monitoring & Logging:** AWS CloudWatch tracks logs, errors, and execution metrics.

---

## AWS S3 Buckets Creation
Amazon S3 is used as the primary data lake for storing raw and processed data. The following S3 buckets are created:

1. **`dataengineeringprojects-sourcefiles`**: Stores raw source data.
2. **`dep-onyoutube-rawprocessed-bronze`**: Stores processed CSV and JSON data.
3. **`dep-onyoutube-rawprocessed-silver`**: Stores cleaned and joined data.
4. **`dep-onyoutube-rawprocessed-gold`**: Stores final aggregated data.
5. **`dep-lambda-triggers`**: Stores trigger files for AWS Lambda execution.

---

## Uploading Raw Data to S3 using AWS CLI
To upload raw data to the S3 bucket, use the following AWS CLI commands:
```sh
aws s3 cp raw_statistics.csv s3://dataengineeringprojects-sourcefiles/
aws s3 cp raw_statistics_reference_data.json s3://dataengineeringprojects-sourcefiles/
```

---

## Raw Data Storage
The project handles two types of raw data:
1. **CSV Data:** `raw_statistics.csv` contains YouTube video statistics.
2. **JSON Data:** `raw_statistics_reference_data.json` provides reference data.

---

## Data Processing with Apache Spark (Scala)
The processing pipeline consists of multiple Spark jobs written in Scala:

1. **Processing CSV Data:** Cleans and stores data in the Bronze bucket.
2. **Processing JSON Data:** Cleans and stores data in the Bronze bucket.
3. **Joining Processed Data:** Merges CSV and JSON data and stores the final output in the Silver bucket.
4. **JAR File Creation:** Spark jobs are compiled into a JAR file using Maven and executed on EMR.

---

## Automating Processing with AWS Lambda
AWS Lambda automates the entire pipeline by triggering EMR jobs:

- Detects file uploads in `dep-lambda-triggers`.
- Launches an EMR cluster.
- Submits Spark jobs for processing.
- Stores results in S3.

The Lambda function uses **Boto3** to interact with AWS services programmatically.

---

## IAM Role & Permissions
To ensure secure access to AWS services, IAM roles are created:
- **`EMR_DefaultRole`**: Allows EMR to interact with S3 and Glue.
- **`EMR_EC2_DefaultRole`**: Grants permissions for EC2 instances running EMR.
- **`LambdaExecutionRole`**: Enables Lambda to trigger EMR clusters and write logs to CloudWatch.

---

## Monitoring with AWS CloudWatch
AWS CloudWatch is used for logging and monitoring:
- Tracks Lambda execution logs.
- Monitors EMR cluster performance.
- Detects errors and triggers alerts.

---

## AWS Glue & Athena Integration
To enable SQL-based querying of processed data:
1. **AWS Glue Crawler** indexes the Silver dataset.
2. **AWS Athena** is used to query processed data efficiently.

---

## Power BI Visualization
Once data is available in AWS Athena, Power BI is used to create dashboards and generate insights from the cleaned YouTube dataset.

---

## Summary of the Workflow
1. **Raw data is uploaded to S3.**
2. **AWS Lambda triggers an EMR cluster.**
3. **Scala Spark jobs process and clean the data.**
4. **Processed data is stored in Bronze and Silver S3 buckets.**
5. **AWS Glue Crawler indexes the data for querying in Athena.**
6. **Power BI is used for visualization.**
7. **CloudWatch monitors the entire pipeline.**

---

This comprehensive documentation provides a clear overview of the YouTube Data Analysis project on AWS, ensuring efficient data processing and insightful analysis. 🚀





************************************************************************************************************************
************************************************************************************************************************
************************************************************************************************************************