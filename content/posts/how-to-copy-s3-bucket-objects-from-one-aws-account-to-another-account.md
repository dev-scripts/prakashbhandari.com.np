---
title: "How to Copy S3 Bucket Objects From One Aws Account to Another Account"
date: 2019-08-24T12:00:15+11:00
draft: false
tags: [
"s3",
"AWS",
]
categories: [
"Cloud",
]
author: "Prakash Bhandari"
cover:
    image: "images/posts/how-to-copy-s3-bucket-objects-from-one-aws-account-to-another-account/how-to-copy-s3-bucket-objects-from-one-aws-account-to-another-account.webp"
    alt: "How to Copy S3 Bucket Objects From One Aws Account to Another Account"
    caption: "How to Copy S3 Bucket Objects From One Aws Account to Another Account"
    relative: true

---

I had to transfer the S3 bucket objects form one AWS account (Source account) to another AWS account (Destination Account) within the same region.
But, transferring the objects form one AWS account to another is not straight forward.

According to AWS documentation its transfer the ownership of S3 objects from one account to another AWS account rather than transferring the objects itself.

To transfer this ownership we have to go through multiple steps. Here, I will explain how to perform this action in few simple steps.

## We should have following

1. Two different AWS accounts (Source account and Destination account).
2. S3 buckets in both account (Source S3 bucket and Destination S3 bucket).
3. Source S3 bucket should have some objects (images, files, videos, etc).
4. Get the destination AWS account number (check AWS official document to find the AWS account).

## Assumption

Just for demo I am assuming following name and region

1. Source bucket name : `prakash_source_s3_bucket`
2. Destination bucket name : `prakash_destination_s3_bucket`
3. Destination AWS account number : `102421234562`
4. Source region name : `ap-southeast-2` (find the list of the AWS [regions](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html) )
5. Destination region name : `ap-southeast-2` (find the list of the AWS [regions](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html) ).

But, you can choose your own bucket name and region name.

## Create and Set-up the Source S3 bucket in Source AWS account

Create the S3 bucket in source account named `prakash_source_s3_bucket`( check the AWS official [document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html) to create the S3 bucket). 
Attach the following policy to the newly created S3 bucket( check the AWS official [document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html) to attach the policy to S3 bucket).

```json
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"DelegateS3Access",
         "Effect":"Allow",
         "Principal":{
            "AWS":"arn:aws:iam::102421234562:root"
         },
         "Action":[
            "s3:ListBucket",
            "s3:GetObject"
         ],
         "Resource":[
            "arn:aws:s3:::prakash_source_s3_bucket/*",
            "arn:aws:s3:::prakash_source_s3_bucket"
         ]
      }
   ]
}
```
![Attach the policy to source S3 bucket](/images/posts/how-to-copy-s3-bucket-objects-from-one-aws-account-to-another-account/attach-the-policy-to-source-s3-bucket.webp#center "Attach the policy to source S3 bucket")

   Image 1 : Attach the policy to source S3 bucket

## Create the Destination S3 bucket in Destination AWS account

Create the S3 bucket in destination account named `prakash_destination_s3_bucket` 
( check the AWS official [document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html) to create the S3 bucket). 
And upload some objects (images, files, videos, etc.)

***Note : we don’t need to set-up any configuration like source S3 bucket.***

## Create and set-up an IAM user in Destination AWS account

Create an IAM user in the destination account (check the official AWS [document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html) on how to create an IAM user) 
and attach the policy to the newly created IAM user so that we can transfer the objects the destination S3 
bucket(check the official AWS [document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html) on how to attach the policy to IAM user).

```json
{ 
   "Version":"2012-10-17",
   "Statement":[ 
      { 
         "Effect":"Allow",
         "Action":[ 
            "s3:ListBucket",
            "s3:GetObject"
         ],
         "Resource":[ 
            "arn:aws:s3:::prakash_source_s3_bucket",
            "arn:aws:s3:::prakash_source_s3_bucket/*"
         ]
      },
      { 
         "Effect":"Allow",
         "Action":[ 
            "s3:ListBucket",
            "s3:PutObject",
            "s3:PutObjectAcl"
         ],
         "Resource":[ 
            "arn:aws:s3:::prakash_destination_s3_bucket",
            "arn:aws:s3:::prakash_destination_s3_bucket/*"
         ]
      }
   ]
}
```

![Attach policy to the IAM user](/images/posts/how-to-copy-s3-bucket-objects-from-one-aws-account-to-another-account/attach-policy-to-the-Iiam-user.webp#center "Attach policy to the IAM user")

Image 2 : Attach policy to the IAM user

## Create Access key for newly created IAM user

In AWS destination account we need to create the access key ID and Secret access key for newly created IAM user. (to create the access follow the AWS official [document](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html#Using_CreateAccessKey) ).


![Create access key for IAM User](/images/posts/how-to-copy-s3-bucket-objects-from-one-aws-account-to-another-account/create-access-key-for-iam-user.webp#center "Create access key for IAM User") 

Image 3 : Create access key for IAM User

## Install AWS CLI on local machine

Now, we have to install the AWS CLI in our local machine so that we can run AWS command to transfer the objects form source S3 bucket to destination S3 bucket. (Follow the official [document](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) on how to install the AWS CLI on local machine). AWS CLI is available for Windows, Linux, macOS, or Unix.

## Configure AWS CLI for IAM user

Configure the AWS CLI for newly created IAM user on destination account(follow the official [document](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)).

![AWS configuration](/images/posts/how-to-copy-s3-bucket-objects-from-one-aws-account-to-another-account/aws-configuration.webp#center "AWS configuration")

Image 4 : AWS configuration

## Copy S3 objects to another account.

Finally, it’s time to run the AWS CLI command to copy the S3 objects from source account to the destination account. We have to run the below sync command to start the process.

```json
aws s3 sync s3://prakash_source_s3_bucket s3://prakash_destination_s3_bucket --source-region ap-southeast-2 --region ap-southeast-2
```

Once we run the AWS command we can see the file sync process in console. The sync time is depend on the size of the source file.

***Note : do not close the object sync progress console window.***

Now, we learned how to copy S3 bucket objects from one account to the another AWS account(transfer the ownership of S3 objects from one account to another AWS account).

Also, publish Medium https://medium.com/@thebhandariprakash/how-to-copy-s3-bucket-objects-from-one-aws-account-to-another-account-609a482fb931

