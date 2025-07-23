# Azure Data Engineering Project: ADF-Data-Ingestion-Orchestration

## Project Overview

This project showcases a complete data engineering workflow using Azure Data Factory (ADF), including automated data ingestion from REST APIs, conditional data transfer from SQL databases to Azure Data Lake Storage (ADLS), and orchestration of pipeline dependencies through scheduled triggers.

## Problem Statement

![problem-statement](/screenshots-project/problem-statement.png)

## Objectives

1. **Fetch Country Data via REST API**
   - Retrieve country information for India, US, UK, China, and Russia.
   - Save the data in separate JSON files named after each country.

2. **Schedule Pipeline Execution**
   - Automatically run the data ingestion pipeline twice daily at 12:00 AM and 12:00 PM IST.

3. **Conditional Data Copy for Customer Data**
   - Copy customer data from the database to ADLS only if the record count exceeds 500.
   - Trigger a child pipeline to copy product data if the customer record count exceeds 600.

4. **Parameter Passing Between Pipelines**
   - Dynamically pass the customer record count from the parent pipeline to the child pipeline using pipeline parameters.


## Architecture Components

- **Azure Data Factory**: Manages and coordinates data pipelines.
- **Azure SQL Database**: Provides customer and product data.
- **Azure Data Lake Storage (ADLS)**: Stores the ingested data.
- **REST API Integration**: Supplies country metadata.
- **Pipeline Parameters**: Enable data exchange between pipelines.
- **Schedule Trigger**: Executes pipelines automatically at defined intervals.

## Pipeline Details

### 1. Country Data Ingestion Pipeline

- **REST Endpoint**: `https://restcountries.com/v3.1/name/{name}`
- **Countries**: India, US, UK, China, Russia
- **Output**: JSON files saved in ADLS with country names as file names.
- **Error Handling**: Includes retry logic for robustness.

#### Steps to follow

1. **Open Azure Data Factory (ADF):**
   Navigate to the Azure portal and open your ADF instance.

2. **Create a New Pipeline:**
   Click on "Author" to create a new pipeline.

3. **Add a Web Activity:**
   Drag and drop a "Web" activity onto the pipeline canvas.
   Configure it to call the REST API endpoint `https://restcountries.com/v3.1/name/{name}` for each country (India, US, UK, China, Russia).

4. **Loop Through Countries:**
   Use a "ForEach" activity to loop through the list of countries.
   Pass the country name dynamically to the Web activity.

5. **Save Data to ADLS:**
   Add a "Copy Data" activity inside the ForEach loop.
   Configure the sink to save the JSON response to Azure Data Lake Storage (ADLS), using the country name as the file name.


#### Screenshots - Task 1

![1.1](/screenshots-project/fetch-country-data%20(1).png)
![1.2](/screenshots-project/fetch-country-data%20(2).png)
![1.3](/screenshots-project/fetch-country-data%20(3).png)
![1.4](/screenshots-project/fetch-country-data%20(4).png)
![1.5](/screenshots-project/fetch-country-data%20(5).png)
![1.6](/screenshots-project/fetch-country-data%20(6).png)
![1.7](/screenshots-project/fetch-country-data%20(7).png)
![1.8](/screenshots-project/fetch-country-data%20(8).png)
![1.9](/screenshots-project/fetch-country-data%20(9).png)
![1.10](/screenshots-project/fetch-country-data%20(10).png)
![1.11](/screenshots-project/fetch-country-data%20(11).png)
![1.12](/screenshots-project/fetch-country-data%20(12).png)
![1.13](/screenshots-project/fetch-country-data%20(13).png)
![1.14](/screenshots-project/task1-fetch-country-data-activity.png)
![1.15](/screenshots-project/fetch-country-data-output.png)

### 2. Scheduled Trigger

- **Frequency**: Twice daily
- **Timings**: 12:00 AM IST and 12:00 PM IST
- **Configuration**: Uses CRON expressions for scheduling.

#### Steps to follow

1. **Create a New Trigger:**
   Go to the "Manage" tab and select "Triggers."
   Click on "+ New" to create a new trigger.

2. **Configure the Trigger:**
   Set the trigger type to "Schedule."
   Configure it to run twice daily at 12:00 AM and 12:00 PM IST.

3. **Associate the Trigger with the Pipeline:**
   Choose the pipeline created in Step 1 and link it to this schedule.


#### Screenshots - Task 2

![2.1](/screenshots-project/trigger-time-set%20(1).png)
![2.2](/screenshots-project/trigger-time-set%20(2).png)
![2.3](/screenshots-project/task2-12AMPM-activity.png)

### 3. Customer Data Pipeline (Parent)

- **Lookup Activity**: Fetches the record count from the customer table.
- **Condition Check**: If the count exceeds 500, data is copied to ADLS.
- **Child Pipeline Trigger**: Invokes the product data pipeline if the customer count exceeds 600.
- **Parameter Passing**: Customer count is passed to the child pipeline.

#### Steps to follow

1. **Create a New Pipeline:**
   Click on "Author" to create a new pipeline for copying customer data.

2. **Add a Lookup Activity:**
   Drag a "Lookup" activity onto the canvas.
   Configure it to fetch the record count from the customer table in the database.

3. **Add an If Condition Activity:**
   Drag an "If Condition" activity onto the canvas.
   Set the condition to check if the record count is greater than 500.

4. **Add a Copy Data Activity:**
   Inside the "If Condition" activity, add a "Copy Data" activity.
   Configure the source as the customer table and the sink as ADLS.

5. **Add an Execute Pipeline Activity:**
   Inside the "If Condition" activity, add an "Execute Pipeline" activity.
   Set it to trigger the child pipeline and pass the customer record count as a parameter, greater than 600.


#### Screenshots - Task 3

- Source <br />
![3.1](/screenshots-project/custdatatable.png)
![3.2](/screenshots-project/productdatatable.png)

- Sink <br />
![3.3](/screenshots-project/adls_custdata.png)
![3.4](/screenshots-project/adls_productdata.png)

- Pipeline Overview <br />
![3.5](/screenshots-project/pipelinetask3.png)
![3.6](/screenshots-project/task3-4parentchildpipeline-activity.png)

### 4. Product Data Pipeline (Child)

- **Condition Check**: Executes only if the customer count parameter exceeds 600.
- **Data Copy**: Copies product table data to ADLS.
- **Parameter Validation**: Uses If Condition activity for validation.

#### Steps to follow

1. **Create a New Pipeline:**
   Click on "Author" to create a new pipeline for copying product data, which will act as child pipeline.

2. **Add a Parameter:**
   Define a parameter in this child pipeline to receive the customer record count from the parent pipeline.

3. **Add an If Condition Activity:**
   Drag and drop an "If Condition" activity onto the canvas.
   Set the condition to check if the customer record count parameter is greater than 600.

4. **Add a Copy Data Activity:**
   Inside the "If Condition" activity, add a "Copy Data" activity.
   Set the source as the product table and the sink as ADLS.


#### Screenshots - Task 4

![4.1](/screenshots-project/pipelinetask4.png)
![4.2](/screenshots-project/task3-4parentchildpipeline-activity.png)

## Sample Data Generation

- **Customer Table**: 700 sample records.
- **Product Table**: 700 sample records.
- **Fields**: Includes IDs, names, contact details, categories, prices, and additional attributes.

## Monitoring and Logging

- **Pipeline Activity Tracking**: Monitored through ADF's built-in tools.
- **Error Alerts**: Managed with retry logic and execution history.
- **Trigger Logs**: Provide details on duration, status, and timestamps.


## Key Features

- Smart execution using conditional logic.
- Dynamic parameter flow across linked pipelines.
- Scheduling aligned with specific time zones.
- Modular design for reusability.
- Scalable framework to integrate more data sources.


## Configuration Checklist

- [x] Azure Data Factory setup.
- [x] ADLS linked and writable.
- [x] Service Permissions established.
- [x] SQL Database and Tables configuration.
- [x] REST API Dataset configuration.
- [x] CRON Expression verified.

## Troubleshooting Guide

| Issue | Suggested Fix |
|-------|---------------|
| API Timeouts | Apply retry policies and verify endpoint availability. |
| SQL Connection Fails | Check firewall settings and linked service configs. |
| Parameter Errors | Ensure correct data types and parameter mappings. |
| Trigger Not Executing | Validate CRON expression and time zone configuration. |

## Future Enhancements

- Integrate data validation mechanisms.
- Apply incremental data copy strategy.
- Set up email or Teams-based notifications.
- Extend to ingest dynamic country lists.
- Implement cold storage archiving for historical exports.

## Author

**Devansh Vats**
Data Engineering