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
  "Scaling Nodejs App using Kubernetes",
  "Node.js microservices",
]
cover:
  image: "images/posts/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs-cover-image.png" # image path/url
  alt: "Scaling Kubernetes Jobs with KEDA: Triggered by Amazon SQS"
  caption: "Scaling Kubernetes Jobs with KEDA: Triggered by Amazon SQS"
  relative: false
  author: "Prakash Bhandari"
description: "Sometimes Kubernetes Jobs need to be scaled to handle long-running tasks, such as processing files in real-time after an upload. Your application might need to process thousands of files, and processing multiple files within a single deployment may not be efficient. In this case, it is better to have an individual jobs to process each file separately"
---

In this post, I will create a small Node.js app containing the publisher and message processor, 
and publish it as an image to Docker Hub.

Then, I will use the same image to demonstrate scaling Kubernetes Jobs using KEDA, triggered by Amazon SQS.

For demonstration purposes, I will be using a Minikube local Kubernetes cluster, 
but in reality, this would be done in a production Kubernetes cluster

Here is the architectural diagram for our workflow.

![Scaling Kubernetes Jobs with KEDA: Triggered by Amazon SQS architecture diagram](/images/posts/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs.png#center)

In the above diagram, we can see that the producer (pod) is publishing a message, which will be done by running a CLI to input the prompt and push the message to Amazon SQS.

When a message arrives in the queue, KEDA will create a job for each message that lands on the Amazon SQS queue.
If there are no messages, no jobs are created. Each job will pull one message from the queue and process it.


## Introduction

KEDA stands for Kubernetes (k8s) Event-Driven Autoscaling.
With KEDA, you can scale any deployment or jobs in Kubernetes according to the number of events
that need to be processed.

Sometimes Kubernetes Jobs need to be scaled to handle long-running tasks, such as processing files in real-time after an upload.
Your application might need to process thousands of files, and processing multiple files within a single deployment may not be efficient. In this case, it is better to have an individual job to process each file separately.

Sometimes Kubernetes Jobs need to be scaled to handle long-running tasks, 
such as processing files in real-time after an upload. Your application 
might need to process thousands of files, and processing multiple files 
within a single deployment may not be efficient. 
In this case, it is better to have an individual jobs to process each file separately.

>In my professional life, I have used KEDA to scale Kubernetes jobs as an alternative to Amazon Web Services(AWS) Lambda functions for processing long-running jobs.

## Steps

### Create Node App

First, let's create a Node.js app with the folder structure shown below. You'll also need to install the `aws-sdk` npm package.

![Folder structure](/images/posts/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs/project-folder-structure.png#center)

Your `package.json` file will look like as below file.
```json
{
  "name": "keda-sqs",
  "version": "1.0.0",
  "description": "Scaling Kubernetes Jobs with KEDA: Triggering by Amazon SQS",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "aws-sdk": "^2.1691.0"
  }
}
```

#### Create Landing Page
Let's create the `index.js` file and start a server, 
which will serve as the main entry point for our app. 
I am creating it to ensure that our app runs without any issues.

```js
import http from 'http';

console.log('Project started.');

const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello, World!\n');
});

server.listen(8080, () => {
    console.log('Server running on port 8080');
});
```

#### Load AWS configuration
Create an `awsConfig.js` file to load AWS credentials from environment variables. 
The environment variables are populated from Kubernetes secrets, which we will create later in this post.

```js
import AWS from 'aws-sdk';

const configureAWS = () => {
    AWS.config.update({ 
        region: process.env.AWS_REGION,
        accessKeyId: process.env.AWS_ACCESS_KEY_ID,
        secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
    });
};
export default configureAWS;
```

#### Create Publisher
Let's create a publisher (`publisher.js`) that will read input from the CLI and publish messages to AWS SQS.
```js
import AWS from 'aws-sdk';
import readline from 'readline';
import configureAWS from './awsConfig.js'; 
configureAWS();

const sqs = new AWS.SQS();

const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
});

const sendMessage = (messageBody) => {
    const params = {
        MessageBody: messageBody, 
        QueueUrl: process.env.SQS_QUEUE_URL, 
    };

    sqs.sendMessage(params, (err, data) => {
        if (err) {
            console.error('Error sending message:', err);
        } else {
            console.log('Message sent successfully, MessageId:', data.MessageId);
            console.log('Message sent successfully \n');
        }
    });
};

rl.question('Enter the message to send to SQS: \n \n ', (messageBody) => {
    sendMessage(messageBody);
    rl.close();
});
```

You need to SSH into the container and run `node publisher.js`, 
which will prompt for message input in the CLI and publish it to SQS.

#### Create Processor 
Let's create a processor (processor.js) that will read message from the SQS process it and delete form SQS.
```js

import AWS from 'aws-sdk';
import configureAWS from './awsConfig.js'; 

configureAWS();

const sqs = new AWS.SQS();
const queueURL = process.env.SQS_QUEUE_URL;

const receiveMessages = async () => {
    const params = {
        QueueUrl: queueURL,
        MaxNumberOfMessages: 10,
        WaitTimeSeconds: 20,
    };

    try {
        const data = await sqs.receiveMessage(params).promise();
        if (data.Messages) {
            await Promise.all(data.Messages.map(async (message) => {
                console.log('Received message:', message.Body);
                //delete the processed message.
                await deleteMessage(message.ReceiptHandle);
            }));
        } else {
            console.log('No messages to process.');
        }
    } catch (error) {
        console.error('Error receiving messages:', error);
    }
};


const deleteMessage = async (receiptHandle) => {
    const params = {
        QueueUrl: queueURL,
        ReceiptHandle: receiptHandle,
    };

    try {
        await sqs.deleteMessage(params).promise();
        console.log('Message deleted successfully');
    } catch (error) {
        console.error('Error deleting message:', error);
    }
};

receiveMessages();
```
To ensure that your processor is processing messages, 
you need to check the logs, which can be done by running the command: 
`kubectl logs {pod name}`
#### Dockrize the app

To Dockerize the app, you need to create a `Dockerfile` in the root of the project and add the following code. 
The code installs Node.js version 19 and exposes port 8081.

```dockerfile
FROM node:19

# Create app directory
WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 8081
CMD [ "node", "index.js" ]
```

After completing all the steps above, run the following command in the terminal to build the image:
```
docker build -t thebhandariprakash/keda-sqs-processor:latest .
```

#### Sharing Docker image with a registry

There are many registry to publish docker images.
Here, I am using [Docker Hub](https://hub.docker.com/) to publish image publicly.

##### Login to Docker Hub
Use below command to login to the Docker Hub. This command will help you to login in the Docker Hub.
```
docker login
```
##### Push the Image to the Registry:
```
docker push <your-registry-username>/<your-repository-name:tag>
```

In my case, my docker push command will be like:

```
docker push thebhandariprakash/keda-sqs-processor:latest
```
The app is created, and the image is published to the public registry. Now, we need to focus on the actual work,
which is scaling the processor job using K8S KEDA, triggered by Amazon SQS.

I will explain how this can be done in the following steps:
### Install Minikube
I will use Minikube to run the job locally on my machine. 
If you don't have Minikube on your machine, download and install [download and install](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Farm64%2Fstable%2Fbinary+download)

### Install KEDA 
Once your minikube is running install KEDA via the below command.
```
kubectl apply -f https://github.com/kedacore/keda/releases/download/v2.10.0/keda-2.10.0.yaml
```

### Create SQS queue and IAM User
In AWS, you need to create an SQS queue, IAM User, and attach the `AmazonSQSFullAccess` policy.
Once you create an IAM user you will get `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`. You need to keep it safe and used in your Secret.

Now, need to create the manifest files for k8s. All the files will be created inside **k8s** folder
### Secret manifest
There are three types of [Authentication Parameters](https://keda.sh/docs/2.14/scalers/aws-sqs/) in KEDA:
1. Pod identity based authentication
2. Role based authentication
3. Credential based authentication

Here, I am using 3rd one (Credential based authentication) which will require above created
`AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.

Let me create a Kubernetes Secret manifest `aws-secrets.yaml` to stores `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` (sensitive information) in a secure way.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: aws-secrets
data:
  AWS_ACCESS_KEY_ID: {base64 encoded aws access key id}
  AWS_SECRET_ACCESS_KEY: {base64 encoded aws secret access key}
  AWS_REGION: {base64-encoded-region}
  SQS_QUEUE_URL: {base64-encoded-sqs-url}
```

If you don’t know how to encode the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`,
you can use the terminal to encode the keys in `base64` as shown below.
``` 
echo -n 'your-access-key-id' | base64
echo -n 'your-secret-access-key' | base64
echo -n 'base64-encoded-region' | base64
echo -n 'base64-encoded-sqs-url' | base64
```

Run the below command to create the secrets:
```text
kubectl apply -f aws-secrets.yaml
```

If successful, result will be like this:
```text  
secret/aws-secrets created
```

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
      name: aws-secrets             # Required.
      key: AWS_ACCESS_KEY_ID        # Required.
    - parameter: awsSecretAccessKey # Required.
      name: aws-secrets             # Required.
      key: AWS_SECRET_ACCESS_KEY    # Required.
```

Run the below command to create the secrets:
```text
kubectl apply -f keda-trigger-auth-aws-credentials.yaml
```

If successful, result will be like this:

```text
kubectl apply -f keda-sqs-processor.yaml
scaledjob.keda.sh/keda-trigger-auth-aws-credentials.yaml created
``` 
### ScaledJob Manifest
Create a `keda-sqs-processor.yaml` file that will pull the image we published earlier to Docker Hub and scale the processor job as soon as a message lands in Amazon SQS.
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
            image: thebhandariprakash/keda-sqs-processor:latest
            command: ["node", "processor.js"]
            env: # pass env value to container form secret
              - name: SQS_QUEUE_URL
                valueFrom:
                  secretKeyRef:
                    name: aws-secrets
                    key: SQS_QUEUE_URL
              - name: AWS_ACCESS_KEY_ID
                valueFrom:
                  secretKeyRef:
                    name: aws-secrets
                    key: AWS_ACCESS_KEY_ID
              - name: AWS_SECRET_ACCESS_KEY
                valueFrom:
                  secretKeyRef:
                    name: aws-secrets
                    key: AWS_SECRET_ACCESS_KEY
              - name: AWS_REGION
                value: "ap-southeast-2" # change your region
        restartPolicy: Never
  pollingInterval: 30  # Check the queue every 30 seconds
  successfulJobsHistoryLimit: 5
  failedJobsHistoryLimit: 5
  minReplicaCount: 0  # Maximum number of concurrent jobs
  maxReplicaCount: 50  # Maximum number of concurrent jobs
  triggers:
    - type: aws-sqs-queue
      authenticationRef:
        name: keda-trigger-auth-aws-credentials 
      metadata:
        queueURL: 'https://sqs.ap-southeast-2.amazonaws.com/{account no}/{que name}' # update the url to your URL
        awsRegion: 'ap-southeast-2'
        queueLength: "5"  # Scale up when there are at least 5 messages
```

Run the command to create the processor scaled job:
```text
kubectl apply -f keda-sqs-processor.yaml
```

If successful, result will be like this:
```text
scaledjob.keda.sh/keda-trigger-auth-aws-credentials.yaml created
``` 

Once you completed the above steps run the below command to see the created scaled job.
```
kubectl get scaledjob 
```
If successful, the result will be like this in the console:
```yaml
NAME                     MIN   MAX   TRIGGERS        AUTHENTICATION                      READY   ACTIVE   AGE
sqs-processor-job        0     50    aws-sqs-queue   keda-trigger-auth-aws-credentials   True    True     15m
```

### Create Publisher Deployment Manifest
Create a `sqs-publisher.yaml` file that will pull the image we published earlier to Docker Hub and runs the publisher CLI app.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqs-publisher
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqs-publisher
  template:
    metadata:
      labels:
        app: sqs-publisher
    spec:
      containers:
      - name: sqs-publisher
        image: thebhandariprakash/keda-sqs-processor:latest
        env: # pass env value to container form aws-secrets that was created earlier
        - name: AWS_ACCESS_KEY_ID
          valueFrom:
            secretKeyRef:
              name: aws-secrets
              key: AWS_ACCESS_KEY_ID
        - name: AWS_SECRET_ACCESS_KEY
          valueFrom:
            secretKeyRef:
              name: aws-secrets 
              key: AWS_SECRET_ACCESS_KEY
        - name: AWS_REGION
          value: "ap-southeast-2" 
        - name: SQS_QUEUE_URL
          valueFrom:
            secretKeyRef:
              name: aws-secrets
              key: SQS_QUEUE_URL
```

Run the command to create the sqs-publisher deployment :
```text
kubectl apply -f sqs-publisher.yaml
```

If successful, result will be like this:
```text
deployment.apps/sqs-publisher created
```

### Verify Processor Scaled Jobs and  Publisher
#### Verify Publisher
Publisher will be running in the pod. To see the running pods by executing the below command.
```cli
kubectl get pods
```
If successful, result will be like in console:
```text
NAME                                 READY   STATUS      RESTARTS   AGE
sqs-publisher-84b88dc649-b9g8f       1/1     Running     0          12h
```
You can ssh into the pods by executing the below command:
```
kubectl exec -it sqs-publisher-84b88dc649-b9g8f -- /bin/bash
```

To publish message form publisher pod you need to execute below commend in the container
```
root@sqs-publisher-84b88dc649-b9g8f:/usr/src/app# node publisher.js 
```
The above command will prompt you to input a message from the terminal that you want to publish to AWS SQS.

If successful, you will see result like this:
```text
Enter the message to send to SQS: 
message1
Message sent successfully, MessageId: 2cb42418-e1b6-4493-b313-b2e96ed3ff14
Message sent successfully
```
#### Verify Processor Scaled Jobs
To see the running/successful/failed jobs you can run below command.
```cli
kubectl get jobs
```
If successful, result will be like in console:
```text
NAME                                COMPLETIONS   DURATION   AGE
keda-sqs-processor-job-982kg        1/1           8s         11s
keda-sqs-processor-job-ft4jw        1/1           6s         41s
keda-sqs-processor-job-nz56t        1/1           5s         11s
keda-sqs-processor-job-qffk4        1/1           8s         41s
```
If you have any GUI tool, you can also view it under Pods. I can see it in Lens, as shown in the image below. Here you will see running jobs, failed jobs, and successful jobs.
The number of successful and failed jobs can be defined in the ScaledJob Manifest file ie. `successfulJobsHistoryLimit: 5` `failedJobsHistoryLimit: 5`
![Publish Message from SQS](/images/posts/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs/keda-scaled-jobs.png#center)

New jobs will be created until you purge the SQS or delete the jobs after processing them from the app.

you can see the logs from processor job by running the below command.
```
kubectl logs keda-sqs-processor-job-982kg
```
If successful, result will be like this:
```
kubectl logs keda-sqs-processor-job-qwd62-8kzgq
Received message: message1
Message deleted successfully
```

### Publish Message from SQS 
You can also use the AWS UI to publish messages to SQS as below. 
This is only the alternative option.

![Publish Message from SQS](/images/posts/scaling-kubernetes-jobs-with-keda-triggered-by-amazon-sqs/publish-message-to-sqs.png#center)


## Conclusion
Scaling Kubernetes jobs with KEDA provides an efficient way to handle event-driven 
workloads, such as processing messages from an Amazon SQS queue.
By using Kubernetes KEDA, you can dynamically scale jobs based on the number 
of incoming messages. This solution is ideal for scenarios where long-running 
tasks need to be processed individually.

In this post, I created a small Node.js app containing the 
publisher and message processor, and published it as an image to Docker Hub.

Then, I used the same image to demonstrate scaling Kubernetes jobs using KEDA,
triggered by Amazon SQS. I used a Minikube local K8s cluster for demonstration 
and explained the steps, which can be easily replicated in
a production Kubernetes cluster to handle large volumes of long-running tasks as individual jobs.

## GitHub Repository
Here is the GitHub repository used for demonstration purposes; you can clone it and create the app in your own way.
https://github.com/dev-scripts/keda-sqs


