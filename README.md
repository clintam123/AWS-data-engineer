
# AWS Course from learndataengineering.com
# Introduction & Goals
- Write AWS pipelines for E-Commerce dataset
  - Data Ingestion(API, Lambda, Kinesis)
  - Stream to Raw S3 Storage
  - Stream to DynamoDB
  - Visualization API for DynamoDB
  - Stream to Redshift Data Warehouse
  - Bath Processing Pipeline for Bulk import

# Contents

- [The Data Set](#the-data-set)
- [Used Tools](#used-tools)
  - [Connect](#connect)
  - [Buffer](#buffer)
  - [Processing](#processing)
  - [Storage](#storage)
  - [Visualization](#visualization)
- [Pipelines](#pipelines)
  - [Stream Processing](#stream-processing)
    - [Storing Data Stream](#storing-data-stream)
    - [Processing Data Stream](#processing-data-stream)
  - [Batch Processing](#batch-processing)
  - [Visualizations](#visualizations)
- [Demo](#demo)
- [Conclusion](#conclusion)
- [Follow Me On](#follow-me-on)
- [Appendix](#appendix)


# The Data Set
- E-Commerce dataset (actual transactions from UK retailer): https://www.kaggle.com/datasets/carrie1/ecommerce-data
- Why I choose this dataset:
  - Great size 43MB (lots of rows instead of columns) -> simulate a streaming over a few minutes
  - CSV file
  - Time series data (invoice date)
  - Text column (description, country) -> search index/ further processing

# Used Tools
## Client
- Python client (send JSON string to API Gateway)
## Connect:
- API Gateway
- AWS Lambda (process JSON send to API Gateway from client then send to buffer)
## Buffer
- AWS Kinesis
- Producer: Lambda in Connect phase
- Consumer: Lamda/ Kinesis Firehose
## Processing
### Stream Processing
- Lambda Functions with triggers on Souce
- Continuous Process
### Batch Processing
- AWS Glue for ETL
- Cloudwatch for Scheduling
## Storage
- S3 File Storage (Data lake)
- DynamoDB (Wide column store, OLTP)
- Redshift Data warehouse (OLAP)
## Visualization
- API
- PowerBI

# Pipelines
- Explain the pipelines for processing that you are building
- Go through your development and add your source code
## Data Ingestion
- Python client (simulate streaming, sends CSV rows as JSON) -> API Gateway URL -> Lambda -> Kinesis
## Stream to S3 Raw Storage
- Kinesis insert triggers Lambda for S3
- Configure Lambda waits for sometime before writes all messages in queue to S3 bucket as file
- Kinesis Data Stream -> Lambda -> S3 Bucket
## Stream to DynamoDB
- Kinesis insert triggers Lambda for DynamoDB
  - Lambda reformates/preprocesses messages
  - Lambda writes customer data (customer + invoices)
  - Lambda writes invoice data (invoice + stock code)
## Visualization API
- Client -> API(Invoice/Customer) -> Lambda Processing -> DynanoDB Invoices/Customers table
## Visualization Redshift
- Kinesis Firehose Delivery Stream connects to Kinesis Data Stream, auto stream data to Redshift
- Firehose writes data into intermediate S3 Bucket
- Firehose copies data from S3 into Redshift table
- PowerBI for analyst access
## Batch Processing for Bulk Import
- Glue Crawlers crawl S3 & Redshift
- Crawlers create Glue catalogs
- Glue Spark Job
- Glue reads from S3 from  