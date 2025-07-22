# Azure Data Engineering Project: ADF-Data-Ingestion-Orchestration

## Project Overview

This project showcases a complete data engineering workflow using Azure Data Factory (ADF), including automated data ingestion from REST APIs, conditional data transfer from SQL databases to Azure Data Lake Storage (ADLS), and orchestration of pipeline dependencies through scheduled triggers.

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

### 2. Scheduled Trigger

- **Frequency**: Twice daily
- **Timings**: 12:00 AM IST and 12:00 PM IST
- **Configuration**: Uses CRON expressions for scheduling.

### 3. Customer Data Pipeline (Parent)

- **Lookup Activity**: Fetches the record count from the customer table.
- **Condition Check**: If the count exceeds 500, data is copied to ADLS.
- **Child Pipeline Trigger**: Invokes the product data pipeline if the customer count exceeds 600.
- **Parameter Passing**: Customer count is passed to the child pipeline.

### 4. Product Data Pipeline (Child)

- **Condition Check**: Executes only if the customer count parameter exceeds 600.
- **Data Copy**: Copies product table data to ADLS.
- **Parameter Validation**: Uses If Condition activity for validation.

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