# youtube-analysis-project

complete architecture to follow:
![image](https://user-images.githubusercontent.com/66850958/225360550-0e50dddd-9797-41f4-8907-13a329cf7940.png)


step1: configure aws using aws cli in local
![image](https://user-images.githubusercontent.com/66850958/225161817-7959e5f4-ad16-47b8-beb8-67566f1f3157.png)


step2: create s3 buckets and upload files to s3 bucket using aws cli.
![image](https://user-images.githubusercontent.com/66850958/225358142-4e544113-0352-46fe-a8dd-921f4ea1c177.png)

step3: create glue crawler to crawl data from these files with iam role to access s3 buckets.
![image](https://user-images.githubusercontent.com/66850958/225358652-2e5223e9-3d38-419c-a4df-379b30aa74cb.png)

step4: Create lambda function to convert json into normalized parquet to be read by athena. the code would be available in lambda_function.py file.
Added awsdarawrangler module as a layer to support execution and attached iam role with s3 and glue policies.
![image](https://user-images.githubusercontent.com/66850958/225359428-d936d7fa-38e3-44ce-9db5-00c0761d218c.png)

step5: create glue crawler for csv raw data.
![image](https://user-images.githubusercontent.com/66850958/225361481-562c3fc2-a9f7-470a-bfb0-f6a539f34130.png)

step6: Writing sql query in athena to join csv and cleaned parquet files based on region key to easily filter out records.
![image](https://user-images.githubusercontent.com/66850958/225361047-14ff5e2c-e588-4ebf-b235-17938e201deb.png)


step7: create etl job to automate process to fetch all csv raw files of all regions. the glue script is etl_job.py 
#Here we are only pushing gb us and ca region folder files for simplicity. 
![image](https://user-images.githubusercontent.com/66850958/225362453-65debaf4-62c4-4f8f-8efc-c4c1bb6e89b5.png)

