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


# AWS Secrets Manager

{
  "fusion_snowflake": {
    "username": "your_user",
    "password": "your_password",
    "account_name": "your_account.region"
  }
}

# IAM Permissions

Attach these policies to Lambda:

AmazonS3FullAccess
AWSLambda_FullAccess
AmazonEventBridgeFullAccess
SecretsManagerReadWrite

# Snowflake Setup

Run snowflake/setup.sql to create:

CURRENCY_DB (Database)
CURRENCY (Schema)
EXCHANGE_RATES_RAW — Raw JSON storage
EXCHANGE_RATES_STG — Flattened staging table
EXCHANGE_RATES — Final analytics table
SP_EXCHANGE_RATE_LOADING — Data pipeline procedure

# Data Processing Flow
Stored Procedure Logic:

Load raw JSON into RAW table
Flatten JSON into STAGING
Merge into FINAL table (deduplicated)

# Validation Queries

-- Raw data
SELECT * FROM CURRENCY.EXCHANGE_RATES_RAW;

-- Staging data
SELECT * FROM CURRENCY.EXCHANGE_RATES_STG;

-- Final table
SELECT * FROM CURRENCY.EXCHANGE_RATES;

-- Hourly validation
SELECT timestamp_utc, COUNT(*) AS currency_pairs
FROM CURRENCY.EXCHANGE_RATES
GROUP BY timestamp_utc
ORDER BY timestamp_utc DESC;

# Tech Stack

| Layer          | Technology               |
| -------------- | ------------------------ |
| Compute        | AWS Lambda (Python 3.11) |
| Scheduling     | AWS EventBridge          |
| Storage        | Amazon S3                |
| Secrets        | AWS Secrets Manager      |
| Data Warehouse | Snowflake                |
| Data Source    | OpenExchangeRates API    |

# Outcomes
Fully automated, production-ready pipeline
Clean and scalable data architecture
Real-time exchange rate analytics
Hands-on experience with AWS + Snowflake integration
















