# Project-3-Incremental-File-Loading-using-Microsoft-Fabric-Pipelines
This project demonstrates how to design and implement an incremental file ingestion pipeline using Microsoft Fabric Pipelines, Azure Data Lake Gen2, Lakehouse, and Warehouse.

# Objective
Efficiently load only new files from ADLS Gen2 into the Warehouse by tracking processed files and avoiding duplicate loads.

# Architecture Overview
Source: Azure Data Lake Gen2
Intermediate: Lakehouse
Target: Warehouse
Tracking Table: file_tracker

# Pipeline Workflow
--------------------------------------------------------------------------------------------------------------------------------------------------------------
1. Get Metadata Activity

Retrieves all files from ADLS Gen2 using childItems.

2. Lookup Activity

Fetches already processed file names from file_tracker table stored in the Warehouse.

SELECT fileName FROM file_tracker;


3. Filter Activity

Filters out files that already exist in the tracking table.

@not(contains(string(activity('Lookup1').output.value),item().name))


4. ForEach Activity

Iterates over newly detected files only.

5. Inside ForEach:
Copy Activity 1: ADLS Gen2 → Lakehouse
Copy Activity 2: Lakehouse → Warehouse (product_dim)

Script Activity

Inserts successfully processed file names into file_tracker with load time.

INSERT INTO file_tracker (fileName, load_time)
VALUES ('@{item().name}', GETUTCDATE());

---------------------------------------------------------------------------------------------------------------
# Technologies Used
Microsoft Fabric Pipelines
Azure Data Lake Gen2
Lakehouse
Warehouse (SQL)


Copy Activity 1: ADLS Gen2 → Lakehouse
