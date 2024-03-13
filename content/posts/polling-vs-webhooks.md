---
title: "Polling vs Webhooks"
date: 2024-03-13T18:31:12+11:00
showToc: true
TocOpen: true
tags : [
  "Webhooks",
  "Pooling",
  "System Design",
  "Software Development",
]
categories : [
  "System Design",
  "Software Development"
]
keywords: [
  "Polling vs Webhooks",
  "Differance between pooling and Webhooks",
  "API Pooling",
  "Webhooks",
]
cover:
  image: "images/posts/polling-vs-webhooks/polling-vs-webhooks.svg" # image path/url
  alt: "Polling vs Webhooks"
  caption: "Polling vs Webhooks"
  relative: false
author: "Prakash Bhandari"
description: "Sometimes, we need to notify or update another system after a certain interval or upon completing background processing jobs. In such situations, we either use webhooks or polling. Each method has it's own use cases, pros, and cons. This blog post will explain the differences between polling and webhooks, along with their respective use cases, advantages, and disadvantages."
---

Sometimes, we need to notify or update another system after a certain interval or upon completing background processing jobs. 
In such situations, we either use webhooks or polling. 
Each method has it's own use cases, advantages, and disadvantages. 
This blog post will explain the differences between polling and webhooks, along with their respective use cases, pros, and cons.

## What is Pooling?
Polling, also known as API polling, is a mechanism where the client repeatedly calls the server to check for updates or changes until it receives a response from the server.

As shown in the diagram above, the client (Service A) continually requesting "Any Update?" to the server (Service B) until it receives a "Yes" response from the server. 


### When to use Polling?
Polling can be used in situations where:
1. When you don't need real-time updates
2. The updates are frequent.
### Pros of Polling
1. *Flexible:* Developer has control on how and when to fetch the data. 
### Cons of Polling
1. *No real-time data :* There's a delay between data creation and receiving an update.
2. *Inefficient:* Consumes more resources and is inefficient as it continuously makes requests to the server at specified intervals even there is no new data.
3. *Scaling Challenges:* Scaling can be challenging, especially when high throughput is required, and the system may struggle to keep up with demand.


## What is Webhook?

A webhook, also known as a reverse API or server-to-client push notification, is a mechanism where the server sends a notification to the client that data has been updated or added on the server. 
This enables real-time communication between the server and the client, facilitating immediate responses to changes in data.

As shown in the diagram above, the client (Service A) sends one request "Tell me when my data will be ready?" to the server (Service B)   until it receives a "Yes Ready" response from the server.
### When to use Webhook?
A webhook can be used in situations where:
1. When you require extremely fresh data.
2. The updates are infrequent.

### Pros of webhooks
1. *Real-time data:* No delay between data create and receiving an update.
2. *Cost-effective:* Resource is only utilized when there is new update in the server. 
### Cons of webhooks
1. *Possibility of missing notifications:*  Possibility of missing notifications due to network issues. There is no automatic retry mechanism if a notification is missed.
2. *In Flexible:* Developers have no control over when or how the data is fetched

## Differance between pooling and Webhooks
Here is a simple table distinguishing between Pooling and webhooks:

|                      | Pooling                                                                                                                      | Webhooks                                                                                            |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Direction of Request | Data are pulled from the server to client on certain interval (Client-to-server  )                                           | Data are pushed form the server to client as soon as data is available (Server-to-client).          |
| Resource Efficiency  | Calls the server even when there are no updates, which means more resource utilization.                                      | Pushed to the client only when changed data is available, which means less resource utilization.    |
| Data freshness       | No fresh data. there's a delay between data creation and receiving an update.                                                | Fetch fresh data.  No delay between data create and receiving an update.                            |
| Risk                 | Scaling can be challenging, especially when high throughput is required, and the system may struggle to keep up with demand. | Possibility of missing notifications with no automatic retry mechanism if a notification is missed. |
| Suitable Use Cases   | Suitable for scenarios where real-time updates are not critical                                                              | Ideal for scenarios requiring immediate responses to events                                         |


This blog post provides a concise overview of polling and webhooks, 
outlining their use-cases and comparing their pros and cons in system design.
Polling offers flexibility but can be resource-intensive, 
while webhooks provide real-time updates with efficient resource utilization. 
The choice between the two depends on specific system requirements and trade-offs.