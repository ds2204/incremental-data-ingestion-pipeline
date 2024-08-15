
# Incremental Data Ingestion Pipeline with AWS RDS, Glue, S3, and Redshift
![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ed43f61-a377-4a7e-9d58-295eaae76e4b/6ffb6619-d5bc-44b1-8a15-c24224d1f9d4/image.png)

## Overview

This project demonstrates an incremental data ingestion pipeline on AWS, focusing on filtering and storing prepaid data in an Amazon Redshift database. The pipeline leverages AWS RDS, AWS Glue for data cataloging and ETL, Amazon S3 for storage, and Amazon Redshift as the final data warehouse.

## Architecture

1. **AWS RDS**: Source database where the original data is stored.
2. **Amazon S3**: Intermediate storage location for data files.
3. **AWS Glue**: 
   - **Crawlers**: Automatically discover and catalog data in S3 and Redshift.
   - **ETL Job**: Transform and filter data, focusing on extracting only prepaid data.
4. **Amazon Redshift**: Final destination for the processed prepaid data.

## Workflow

### 1. **Data Upload to S3**
   - Data is incrementally uploaded to an S3 bucket. This data may contain both prepaid and non-prepaid records.

### 2. **AWS Glue Crawlers**
   - **S3 Crawler**: Scans the uploaded files in S3 and updates the Glue Data Catalog with the new data schema.
   - **Redshift Crawler**: Scans the Redshift database and updates the Glue Data Catalog to reflect the latest schema in the Redshift tables.

### 3. **ETL Process (with Prepaid Filter)**
   - An AWS Glue ETL job is triggered after the data is uploaded and crawled.
   - **Visual Representation in AWS Glue Studio**:
     - The ETL job is visually designed using AWS Glue Studio.
     - **Prepaid Filter**: 
       - A filter transformation is added in the ETL workflow to specifically extract only the prepaid data from the dataset.
       - This filter is configured to identify records where the `plan_type` (or the relevant column) is set to `'Prepaid'`.
     - **Transformation**: Any additional transformations (e.g., renaming columns, changing data types) are applied downstream of the filter.
     - **Load**: The filtered prepaid data is loaded into a designated table in Amazon Redshift.

![Screenshot (248).png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ed43f61-a377-4a7e-9d58-295eaae76e4b/76909603-ffe6-4a79-92db-0af00046168e/Screenshot_(248).png)


### 4. **Data Validation in Redshift**
   - After the ETL process is complete, the Redshift table should contain only prepaid data.
   - Data consistency and accuracy checks are performed to ensure the ETL process was successful.

## Steps to Set Up the Pipeline

### Prerequisites
- AWS account with access to RDS, S3, Glue, and Redshift.
- Proper IAM roles with permissions for Glue to interact with S3, Redshift, and RDS.

### Step 1: Set Up RDS
- Create an RDS instance and populate it with your source data.

### Step 2: Upload Data to S3
- Incrementally upload data files to the designated S3 bucket.

### Step 3: Create Glue Crawlers
- **S3 Crawler**:
  - Configure the crawler to scan the S3 bucket and update the Glue Data Catalog.
  - Schedule the crawler to run after each data upload.
- **Redshift Crawler**:
  - Configure the crawler to scan the Redshift database and update the Glue Data Catalog.
  - Schedule the crawler to run after the ETL job.

### Step 4: Create and Run the Glue ETL Job
- Configure an ETL job in Glue to:
  - Read data from the S3 bucket.
  - Filter the data to retain only prepaid records.
  - Load the processed data into the Redshift table.
- Schedule the ETL job to run after the S3 crawler completes.

### Step 5: Validate Data in Redshift
- Verify that the Redshift table contains only prepaid data after the ETL job completes.

### 1st upload to s3
![Screenshot (264).png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ed43f61-a377-4a7e-9d58-295eaae76e4b/713c6f1a-68a1-4ec8-bb8f-8e82fd3aaf4e/Screenshot_(264).png)

### 2nd upload to s3
![Screenshot (260).png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1ed43f61-a377-4a7e-9d58-295eaae76e4b/b67bdee5-b058-4954-a6f2-170ec667f50e/Screenshot_(260).png)

## Conclusion

This pipeline allows for efficient and automated incremental ingestion of data, ensuring that only relevant prepaid data is stored in the Redshift data warehouse. The combination of AWS services like RDS, S3, Glue, and Redshift provides a scalable and robust solution for handling large datasets with specific filtering requirements.

## Troubleshooting

If the ETL job fails or the expected data is not in Redshift, check the following:
- Ensure that the Glue crawler for S3 ran successfully and updated the Data Catalog.
- Verify the ETL job's filter logic to ensure it's correctly identifying prepaid data.
- Check the Redshift connection and table schema to confirm data was loaded correctly.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
