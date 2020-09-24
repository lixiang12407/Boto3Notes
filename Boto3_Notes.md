[TOC]

# Installation

> pip install boto3
>
> pip install boto3==1.0.0

# Configuration

> aws configure

Or

.aws/credentials：

> [default]
>
> aws_access_key_id = YOUR_ACCESS_KEY
>
> aws_secret_access_key = YOUR_SECRET_KEY

.aws/config：

> [default]
>
> region = us-east-1

# Using Boto3

```python
import boto3

s3 = boto3.resource("s3")

for bucket in s3.buckets.all():
    print(bucket.name)
    
data = open('test.jpg', 'rb')
s3.Bucket('my-bucket').put_object(Key='test.jpg', Body=data)
```

# S3 buckets

## Create buckets

```python
import logging
import boto3
from botocore.exceptions import ClientError


def create_bucket(bucket_name, region=None):
    """Create an S3 bucket in a specified region

    If a region is not specified, the bucket is created in the S3 default
    region (us-east-1).

    :param bucket_name: Bucket to create
    :param region: String region to create bucket in, e.g., 'us-west-2'
    :return: True if bucket created, else False
    """

    # Create bucket
    try:
        if region is None:
            s3_client = boto3.client('s3')
            s3_client.create_bucket(Bucket=bucket_name)
        else:
            s3_client = boto3.client('s3', region_name=region)
            location = {'LocationConstraint': region}
            s3_client.create_bucket(Bucket=bucket_name,
                                    CreateBucketConfiguration=location)
    except ClientError as e:
        logging.error(e)
        return False
    return True
```

## List existing buckets

```python
# Retrieve the list of existing buckets
s3 = boto3.client('s3')
response = s3.list_buckets()

# Output the bucket names
print('Existing buckets:')
for bucket in response['Buckets']:
    print(f'  {bucket["Name"]}')
```

# Uploading files

```python
import logging
import boto3
from botocore.exceptions import ClientError


def upload_file(file_name, bucket, object_name=None):
    """Upload a file to an S3 bucket

    :param file_name: File to upload
    :param bucket: Bucket to upload to
    :param object_name: S3 object name. If not specified then file_name is used
    :return: True if file was uploaded, else False
    """

    # If S3 object_name was not specified, use file_name
    if object_name is None:
        object_name = file_name

    # Upload the file
    s3_client = boto3.client('s3')
    try:
        response = s3_client.upload_file(file_name, bucket, object_name)
    except ClientError as e:
        logging.error(e)
        return False
    return True

s3 = boto3.client('s3')
with open("FILE_NAME", "rb") as f:
    s3.upload_fileobj(f, "BUCKET_NAME", "OBJECT_NAME")
```

# Downloading files

```python
import boto3

s3 = boto3.client('s3')
s3.download_file('BUCKET_NAME', 'OBJECT_NAME', 'FILE_NAME')

s3 = boto3.client('s3')
with open('FILE_NAME', 'wb') as f:
    s3.download_fileobj('BUCKET_NAME', 'OBJECT_NAME', f)
```



# Session reference

```python
class boto3.session.Session(aws_access_key_id=None, aws_secret_access_key=None, aws_session_token=None, region_name=None, botocore_session=None, profile_name=None)

client(service_name, region_name=None, api_version=None, use_ssl=True, verify=None, endpoint_url=None, aws_access_key_id=None, aws_secret_access_key=None, aws_session_token=None, config=None)

resource(service_name, region_name=None, api_version=None, use_ssl=True, verify=None, endpoint_url=None, aws_access_key_id=None, aws_secret_access_key=None, aws_session_token=None, config=None)
```

**endpoint_url**

- `boto3.``client`(**args*, ***kwargs*)[[source\]](https://boto3.amazonaws.com/v1/documentation/api/latest/_modules/boto3.html#client)

  Create a low-level service client by name using the default session.See [`boto3.session.Session.client()`](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/core/session.html#boto3.session.Session.client).

- `boto3.``resource`(**args*, ***kwargs*)[[source\]](https://boto3.amazonaws.com/v1/documentation/api/latest/_modules/boto3.html#resource)

  Create a resource service client by name using the default session.See [`boto3.session.Session.resource()`](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/core/session.html#boto3.session.Session.resource).

```python
# Use the default session
sqs = boto3.client('sqs')
s3 = boto3.resource('s3')

# Custom Session
session = boto3.session.Session(profile_name="name") # can add parameter, like profile
sqs = session.client('sqs')
s3 = session.resource('s3')
```

```python
from boto3.session import Session
import boto3

aws_key = "..."
aws_secret = "..."

session = Session(aws_access_key = aws_key, aws_secret_access_key = aws_secret, region_name="cn-south-1")

s3 = session.resource("s3")
client = session.client("s3")

for bucket in s3.buckets.all():
    print("bucket name : %s" % bucket.name)
    
bucket = "predictionupload"
bojkey = "nj.png"

data = open("nj.png", "rb")
file_obj = s3.Bucket(bucket).put_object(Key=objkey, Body=data)
```

```python

# coding=utf-8
import boto3
 
access_key = 'N01UQBDZP0CTANJA1U2G'
secret_key = 'BoUVH2NAYUSPAXv9zer7vJNekGSRSWjUXwu1lvqK'
s3 = boto3.resource(
    service_name='s3',
    aws_access_key_id=access_key,
    aws_secret_access_key=secret_key,
    endpoint_url='http://192.168.5.34',
    verify=False
)
 
response = s3.create_bucket(Bucket='fufu1')
print(response)
# resource中的五个条件必须全部有，否则会报错InvalidAccessKeyId,如果连接成功会打印输出response
```

