# youtube-analysis-project

step1: configure aws using aws cli in local
![image](https://user-images.githubusercontent.com/66850958/225161817-7959e5f4-ad16-47b8-beb8-67566f1f3157.png)


step2: create s3 buckets and upload files to s3 bucket using aws cli.
![image](https://user-images.githubusercontent.com/66850958/225358142-4e544113-0352-46fe-a8dd-921f4ea1c177.png)

step3: create glue crawler to crawl data from these files with iam role to access s3 buckets.
![image](https://user-images.githubusercontent.com/66850958/225358652-2e5223e9-3d38-419c-a4df-379b30aa74cb.png)

step4:create lambda function to convert json into normalized parquet to be read by athena. the code would be available as  
![image](https://user-images.githubusercontent.com/66850958/225359428-d936d7fa-38e3-44ce-9db5-00c0761d218c.png)
