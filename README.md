# Real-Time-Currency-Exchange-Rate-Pipeline
This project builds an automated exchange rates pipeline using AWS and Snowflake. Data is fetched from an external API via AWS Lambda, stored in Amazon S3, and loaded into Snowflake using a stored procedure. The pipeline organizes data into raw, staging, and final tables for efficient querying.

# Architecture 
<img width="1536" height="1024" alt="Archetechture" src="https://github.com/user-attachments/assets/bd5165ad-15aa-4f7a-a1c2-c36ad0c92bc5" />

# Architecture Overview
EventBridge triggers the pipeline every hour
AWS Lambda fetches exchange rates from API
Raw JSON is stored in Amazon S3
Data is processed and loaded into Snowflake
Stored procedure transforms and deduplicates data

# Key Features

 Fully Serverless — Runs entirely on AWS Lambda (no infrastructure management)
 Automated Hourly Pipeline — Triggered via AWS EventBridge
 Dual Storage Strategy — Raw JSON in S3 + structured data in Snowflake
 Idempotent Loads — MERGE logic ensures no duplicate records
 Secure Secrets Management — Credentials stored in AWS Secrets Manager
 Partitioned S3 Storage — Organized by year/month/day/hour
 Layered Snowflake Architecture — RAW → STG → FINAL for data quality & lineage

 # AWS Infrastructure Setup
🔹 Lambda Function
Runtime: Python 3.11
Architecture: x86_64
Memory: 512 MB (recommended)
Timeout: 30–60 seconds

🔹 Environment Variables

| Variable          | Description            | Example                                                                                        |
| ----------------- | ---------------------- | ---------------------------------------------------------------------------------------------- |
| region_name       | AWS region             | us-east-1                                                                                      |
| snowflake_db      | Snowflake database     | CURRENCY_DB                                                                                    |
| snowflake_role    | Snowflake role         | SYSADMIN                                                                                       |
| snowflake_wh      | Snowflake warehouse    | COMPUTE_WH                                                                                     |
| environment       | Deployment environment | prod                                                                                           |
| s3_bucket_name    | Target S3 bucket       | fx-pulse-data                                                                                  |
| oer_base_url      | API endpoint           | [https://openexchangerates.org/api/latest.json](https://openexchangerates.org/api/latest.json) |
| oer_app_id        | API key                | your_app_id                                                                                    |
| oer_base_currency | Base currency          | USD                                                                                            |
