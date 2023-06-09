# youtube-analysis-project


Overview
This project aims to securely manage, streamline, and perform analysis on the structured and semi-structured YouTube videos data based on the video categories and the trending metrics.


Project Goals


Data Ingestion — Build a mechanism to ingest data from different sources


ETL System — We are getting data in raw format, transforming this data into the proper format


Data lake — We will be getting data from multiple sources so we need centralized repo to store them


Scalability — As the size of our data increases, we need to make sure our system scales with it


Cloud — We can’t process vast amounts of data on our local computer so we need to use the cloud, in this case, we will use AWS


Reporting — Build a dashboard to get answers to the question we asked earlier 

Services we will be using


Amazon S3: Amazon S3 is an object storage service that provides manufacturing scalability, data availability, security, and performance.


AWS IAM: This is nothing but identity and access management which enables us to manage access to AWS services and resources securely.


QuickSight: Amazon QuickSight is a scalable, serverless, embeddable, machine learning-powered business intelligence (BI) service built for the cloud.


AWS Glue: A serverless data integration service that makes it easy to discover, prepare, and combine data for analytics, machine learning, and application development.


AWS Lambda: Lambda is a computing service that allows programmers to run code without creating or managing servers.


AWS Athena: Athena is an interactive query service for S3 in which there is no need to load data it stays in S3.


Dataset Used


This Kaggle dataset contains statistics (CSV files) on daily popular YouTube videos over the course of many months. There are up to 200 trending videos published every day for many locations. The data for each region is in its own file. The video title, channel title, publication time, tags, views, likes and dislikes, description, and comment count are among the items included in the data. A category_id field, which differs by area, is also included in the JSON file linked to the region.

https://www.kaggle.com/datasets/datasnaek/youtube-new

Architecture Diagram


![image](https://user-images.githubusercontent.com/66850958/225383655-89a9595c-36c9-4996-9d5d-abce92a4ecbc.png)


Step by step process:



Step1: Configure aws using aws cli in local


![image](https://user-images.githubusercontent.com/66850958/225161817-7959e5f4-ad16-47b8-beb8-67566f1f3157.png)


Step2: Create s3 buckets and upload files to s3 bucket using aws cli.


![image](https://user-images.githubusercontent.com/66850958/225358142-4e544113-0352-46fe-a8dd-921f4ea1c177.png)


Step3: Create glue crawler to crawl data from these files with iam role to access s3 buckets.


![image](https://user-images.githubusercontent.com/66850958/225358652-2e5223e9-3d38-419c-a4df-379b30aa74cb.png)


Step4: Create lambda function to convert referenced data json into normalized parquet to be read by athena. the code would be available in lambda_function.py file.
Added awsdarawrangler module as a layer to support execution and attached iam role with s3 and glue policies.


![image](https://user-images.githubusercontent.com/66850958/225359428-d936d7fa-38e3-44ce-9db5-00c0761d218c.png)


Step5: Create glue crawler for csv raw data.


![image](https://user-images.githubusercontent.com/66850958/225361481-562c3fc2-a9f7-470a-bfb0-f6a539f34130.png)



Step6: Writing sql query in athena to join csv and cleaned parquet files based on region key to easily filter out records.


![image](https://user-images.githubusercontent.com/66850958/225361047-14ff5e2c-e588-4ebf-b235-17938e201deb.png)


Step7: Create etl job to automate process to fetch all csv raw files of all regions, convert to parquet and store in cleaned s3 bucket. 
The glue script is etl_job.py 


#Here we are only pushing gb us and ca region folder files for simplicity. 


![image](https://user-images.githubusercontent.com/66850958/225362453-65debaf4-62c4-4f8f-8efc-c4c1bb6e89b5.png)


Successful output of above job in s3 with partitioned based on regions.


![image](https://user-images.githubusercontent.com/66850958/225363351-a71c0804-e2bd-4945-b414-a5fb7e0042e5.png)


Step8: Create glue crawler based on these cleaned parquet files.


![image](https://user-images.githubusercontent.com/66850958/225364800-d842e403-4c62-488e-a4c1-47065895f549.png)


Step9: Create lambda trigger to run whenever new file is added in raw_referenced_data folder to be converted to cleaned parquet.


![image](https://user-images.githubusercontent.com/66850958/225365814-798435e6-12f4-4e15-891b-0b9b5450db29.png)


Delete files to test the above configuration and reupload them.


![image](https://user-images.githubusercontent.com/66850958/225366729-25347cfb-81fd-4f05-a32e-c60d98da922a.png)


After reuploading, the cleaned s3 bucket got updated by the new files generated by lambda trigger.


![image](https://user-images.githubusercontent.com/66850958/225367029-6c4a37f6-54d6-4127-aa6a-f7d9dc960e79.png)


Joining query from cleaned json and csv data.


![image](https://user-images.githubusercontent.com/66850958/225369569-0e8f35f9-1217-41bc-b08e-fe79c78d2f4e.png)


Step10: Create etl job to perform above query joining in glue. create a new s3 analytics bucket to store final output.


![image](https://user-images.githubusercontent.com/66850958/225372652-8b86885b-13f2-4a65-9fba-823e503775b5.png)


Successful output in analytics bucket as partitioned on region and category_id.


![image](https://user-images.githubusercontent.com/66850958/225372926-26274fe9-d769-469d-afc2-8da54e8393ce.png)


Output in athena.


![image](https://user-images.githubusercontent.com/66850958/225373262-4384dfe2-999d-4df2-99ad-d704ecbe32de.png)


Step10: Create quicksight account and build a visual report based on analytic bucket data.


![image](https://user-images.githubusercontent.com/66850958/225374336-eb7be635-e2c1-4260-a5be-643d03958a86.png)


Step11: Create a new dataset and connect to athena awsdatacatalog.


![image](https://user-images.githubusercontent.com/66850958/225377248-040a3eeb-7958-4bc6-82bb-917aeca9875a.png)


Make a dashboard and export it as per your need.


![image](https://user-images.githubusercontent.com/66850958/225379132-a8cf9a97-8171-4223-9e8d-f21d4841051f.png)



