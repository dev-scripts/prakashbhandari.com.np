---
title: "Scaling Kubernetes Jobs with KEDA: Triggered by Amazon SQS"
date: 2024-09-28T14:05:05+10:00
draft: false
showToc: true
TocOpen: true
tags : [
  "k8s",
  "Kubernetes",
  "DevOps",
  "KEDA"
]
categories : [
  "DevOps",
  "k8s",
  "Kubernetes"
]
keywords: [
  "Scaling Kubernetes Jobs with KEDA: Triggered by AWS SQS",
  "Scaling Kubernetes Jobs",
  "Alternative to AWS Lambda",
  "Scaling Kubernetes Jobs with KEDA, Triggered by Amazon SQS",
  "Scaling Kubernetes Jobs",
  "KEDA and Amazon SQS",
  "Event-driven autoscaling Kubernetes",
  "Minikube tutorial",
  "Kubernetes jobs scaling tutorial",
  "Using KEDA with SQS",
  "How to scale jobs in Kubernetes",
  "KEDA setup guide",
  "Amazon SQS with Kubernetes",
  "Long-running tasks in Kubernetes",
  "KEDA for event-driven workloads",
  "Kubernetes job processing",
  "KEDA AWS integration",
  "Scaling jobs based on SQS messages",
  "Kubernetes local development with Minikube",
]
cover:
  image: "images/posts/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs-cover-image.png" # image path/url
  alt: "Scaling Kubernetes Jobs with KEDA: Triggered by Amazon SQS"
  caption: "Scaling Kubernetes Jobs with KEDA: Triggered by Amazon SQS"
  relative: false
  author: "Prakash Bhandari"
description: "Sometimes Kubernetes Jobs need to be scaled to handle long-running tasks, such as processing files in real-time after an upload. Your application might need to process thousands of files, and processing multiple files within a single deployment may not be efficient. In this case, it is better to have an individual jobs to process each file separately"
---

In this post, I will demonstrate scaling Kubernetes Jobs using KEDA, triggered by Amazon SQS.

I will use KEDA to run a job for each message that lands on an Amazon SQS queue.

When a message arrives in the queue, KEDA will create a job.
If there are no messages, no jobs are created. Each job will pull one message from the queue and process it.

For demonstration purposes, I will be using a Minikube local cluster, 
but in reality, this would be done in a production Kubernetes cluster

Here is the architectural diagram for our workflow.

![Scaling Kubernetes Jobs with KEDA: Triggered by Amazon SQS architecture diagram](/images/posts/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs.png#center)


## Introduction

KEDA stands for Kubernetes (k8s) Event-Driven Autoscaling.
With KEDA, you can scale any deployment or jobs in Kubernetes according to the number of events
that need processed.

KEDA is used in k8s to enable event-driven scaling of workloads, making it easier to scale 
based on custom events such as messages in a queue (SQS, RabbitMQ, Apache Kafka, etc), changes in a database,
or event triggered from an external system.

Sometimes Kubernetes Jobs need to be scaled to handle long-running tasks, 
such as processing files in real-time after an upload. Your application 
might need to process thousands of files, and processing multiple files 
within a single deployment may not be efficient. 
In this case, it is better to have an individual jobs to process each file separately

## Steps

### Install Minikube
I will use Minikube to run the job locally on my machine. 
If you don't have Minikube in your machine, download and install [download and install](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Farm64%2Fstable%2Fbinary+download)

### Install KEDA 
Once your minikube is running install KEDA via below command.
```
kubectl apply -f https://github.com/kedacore/keda/releases/download/v2.10.0/keda-2.10.0.yaml
```

### Create SQS queue and IAM User
In AWS, you need to crete SQS queue, IAM User and attach the AmazonSQSFullAccess policy.

Once you create a IAM user you will get `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.
You need to keep it safe and used in your Secret.

### Secret manifest
There are three type of [Authentication Parameters](https://keda.sh/docs/2.14/scalers/aws-sqs/) in KEDA:
1. Pod identity based authentication
2. Role based authentication
3. Credential based authentication

Here, I am using 3rd one (Credential based authentication) which will require above created
`AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.

Let me create a Kubernetes Secret manifest `keda-aws-secrets.yaml` to stores `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` (sensitive information) in a secure way.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: keda-aws-secrets
data:
  AWS_ACCESS_KEY_ID: {base64 encoded aws access key id}
  AWS_SECRET_ACCESS_KEY: {base64 encoded aws secret access key}
```

If you don’t know how to encode the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`,
you can use the terminal to encode the keys in `base64` as shown below.
``` 
echo -n "51cjNnhA9Y1NLQVBicJ6sx+1IH2RY63spiGJtk10" | base64 
```

Run command to create the secrets `kubectl apply -f keda-aws-secrets.yaml`

### KEDA TriggerAuthentication Manifest
Need to  create a Kubernetes manifest file `keda-trigger-auth-aws-credentials.yaml` to define a KEDA `TriggerAuthentication` resource,
which is used to securely authenticate triggers in KEDA that require credentials. 
In our case, it configures authentication for an AWS SQS using access keys stored in Kubernetes secrets.
```yaml
apiVersion: keda.sh/v1alpha1
kind: TriggerAuthentication
metadata:
  name: keda-trigger-auth-aws-credentials
spec:
  secretTargetRef:
    - parameter: awsAccessKeyID     # Required.
      name: keda-aws-secrets        # Required.
      key: AWS_ACCESS_KEY_ID        # Required.
    - parameter: awsSecretAccessKey # Required.
      name: keda-aws-secrets        # Required.
      key: AWS_SECRET_ACCESS_KEY    # Required.
```

Run command to create the secrets `kubectl apply -f keda-trigger-auth-aws-credentials.yaml`
### ScaledJob Manifest
Create a `file-sqs-processor.yaml` file 
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledJob
metadata:
  name: keda-sqs-processor-job
spec:
  jobTargetRef:
    template:
      spec:
        containers:
          - name: sqs-processor
            image: thebhandariprakash/keda-sqs-processor:latest # You can have your own Image
            command: ["node", "index.js"] # Running script as after job triggred by SQS to process
        restartPolicy: Never
  pollingInterval: 30  # Check the queue every 30 seconds
  successfulJobsHistoryLimit: 5
  failedJobsHistoryLimit: 5
  minReplicaCount: 10  # Maximum number of concurrent jobs
  maxReplicaCount: 50  # Maximum number of concurrent jobs
  triggers:
    - type: aws-sqs-queue
      authenticationRef:
        name: keda-trigger-auth-aws-credentials
      metadata:
        queueURL: 'https://sqs.{region}.amazonaws.com/{accountId}/{SQSName}'
        awsRegion: 'region' # ie ap-southeast-2
        queueLength: "5"  # Scale up when there are at least 5 messages
```

Run command `kubectl apply -f file-sqs-processor.yaml`

### Verify Scaled job
Once you completed above steps run below command to see the scaled job
```
kubectl get scaledjob 
```
You will see result like this in the terminal: 
```yaml
NAME                     MIN   MAX   TRIGGERS        AUTHENTICATION                      READY   ACTIVE   AGE
keda-sqs-processor-job   0     50    aws-sqs-queue   keda-trigger-auth-aws-credentials   True    True     15m
```

### Publish Message from SQS
You can use the AWS interface to publish messages to SQS. 
Here, I am directly using the AWS interface to publish the message to SQS.
In the real world, the message would typically be published by an application.
![Publish Message from SQS](/images/posts/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs/publish-message-to-sqs.png#center)

### Verify Scaled Jobs
To see the running/successful/failed jobs you can run below command.
```cli
kubectl get jobs
```
Result will be like in console:
```text
NAME                           COMPLETIONS   DURATION   AGE
keda-sqs-processor-job-982kg   1/1           8s         11s
keda-sqs-processor-job-ft4jw   1/1           6s         41s
keda-sqs-processor-job-nz56t   1/1           5s         11s
keda-sqs-processor-job-qffk4   1/1           8s         41s
```
If you have any GUI tool, you can also view it under Pods. 
I can see it in Lens, as shown in the image below. Here you will see running jobs, failed jobs and successful jobs.
Number of successful and failed jobs can be defined in ScaledJob Manifest file ie. `successfulJobsHistoryLimit: 5` `failedJobsHistoryLimit: 5`
![Publish Message from SQS](/images/posts/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs/keda-scaled-jobs.png#center)

New jobs will be created until you purge the SQS or delete the jobs after processing them from the app.

## Conclusion
Scaling Kubernetes jobs with KEDA provides an efficient way to handle event-driven workloads,
such as processing messages from an Amazon SQS queue.
By using  Kubernetes KEDA, you can dynamically scale jobs based on the number of incoming messages.
This solution is ideal for scenarios where long-running tasks need to be processed individually. 

In this post, I have used Minikube local K*S cluster for demonstration and explained the steps, 
which can be easily replicated in a production Kubernetes cluster to handle large volumes of long-running tasks as a individual jobs.

