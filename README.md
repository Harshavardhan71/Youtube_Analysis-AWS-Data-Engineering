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
