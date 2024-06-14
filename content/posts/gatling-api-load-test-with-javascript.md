---
title: "Gatling API Load Test With JavaScript"
date: 2024-06-14T17:57:55+10:00
showToc: true
TocOpen: true
tags : [
  "Gatling",
  "Software Development",
  "JavaScript",
  "Testing",
]
categories : [
  "Software Development",
  "JavaScript",
  "Testing",
]
keywords: [
  "Gatling API Load Test With JavaScript",
  "Load Testing", 
  "Gatling Load Test",
  "JavaScript API Load Testing", 
  "Performance Testing for Applications", 
   "Scalability Testing",
   "Application Stability Testing", 
  "Production Environment Testing", 
    "Gatling Installation Guide", 
    "Gatling Project Setup", 
    "Virtual Users in Gatling", 
    "Handling Seasonal Peaks in Applications",
      "Cloud Application Performance Issues", 
      "API Response Time Analysis"
]
cover:
    image: "images/posts/gatling-api-load-test-with-javascript/gatling-api-load-test-with-javascript.svg" # image path/url
    alt: "Gatling API Load Test With JavaScript"
    caption: "Gatling API Load Test With JavaScript"
    relative: false
author: "Prakash Bhandari"
description: "Ensuring an application runs smoothly in production is crucial. This post guides you to write and execute a simple Gatling API load test using JavaScript to ensure the stability, speed, scalability, and responsiveness of your application  under a given workload."
---

Ensuring an application runs smoothly in production is crucial. This post guides you to write and execute a simple Gatling API load test using JavaScript to ensure the stability, speed, scalability, and responsiveness of your application  under a given workload.

You may have written unit tests and functional tests, and the application looked good, so you shipped it to production. However, it suddenly crashes, experiences performance issues, or encounters API request timeout errors. The reason is simple: the conditions in the test environment and the production environment are not the same. The amount of data in the database, the traffic load, and the architecture in the production environment differ from those in your test environment. As a result production load is always more: 

Production load increase because of following regions :

**Larger Audience** :  You may have millions of users in the production environment.

**Seasonal Peaks** : Let’s say you have an e-commerce portal to sell products. You may experience higher traffic during sales events like Black Friday, Boxing Day, or when running any promotional campaigns.

**Fast Growth** : Business grows faster than expected which will increase the traffic as well as data in the database.

Poor application performance and crashes directly impact sales or even damage the brand.

As developers, our main goal is to ensure the stability, speed, scalability, and responsiveness of an application under a given workload.

People may say that if your application is running in the cloud, you don’t need to worry too much about performance since the infrastructure will automatically autoscale and take care of the application's performance. But this is not 100% true. You may have issues like N+1 queries, no caching, or no pagination, etc. which can still affect performance.

# What is load testing ?
 Load testing is the non-functional software testing technique which determines the stability, speed, scalability, and responsiveness of an application under a specified workload.

Load testing involves simulating the production environment with virtual users to identify bottlenecks in the application, analyze response times, and determine the appropriate infrastructure size for a given workload.
## Types of Load test
There are three types of load tests:
### Capacity Test 
This kind of test is generally used to identify the maximum capacity of your application and how it scales. Keep increasing the number of users per second to find application's breaking point.
### Stress Test 
Identify how the application behaves after sudden load peaks. Does it completely crash, return to normal, or experience latency?
### Soak test 
In a soak test, you will run a high or steady load for a long time and observe how the application behaves. This kind of test helps you identify memory leaks, resource leaks, or degradation that could happen over time.

# Gatling

Gatling is a highly flexible load-testing platform. You can write load tests in Java, Kotlin, Scala and, JavaScript|TypeScript or use no-code feature with Gatling Enterprise. JavaScript Gatling load-testing is only avilabe form Gatling version 3.11.3.


## Prerequisites
- Node.js v18 or later (LTS versions only) and npm v8 or later.
- JVM 

## Run the login App locally
In this post I am going to write a load test script to test the login API: I have already created a application which is avilabe in the GitHub [mysql-express-node-auth-app](https://github.com/dev-scripts/mysql-express-node-auth-app) . You can simply clone in you local machine and run. App will be accessable `http://localhost:8000` and app doc will be accessble `http://localhost:8000/docs`. once you open `http://localhost:8000/docs` you will see below screen in the browser.

![JS App](/images/posts/gatling-api-load-test-with-javascript/js-app.png#center)

**Note:- If you have your own app, you can write tests for it. This app is intended solely for demo purposes.**


## Download Gatling for JavaScript

Once [mysql-express-node-auth-app](https://github.com/dev-scripts/mysql-express-node-auth-app) is up and running in your machine, you can [Download Gatling for JavaScript](https://docs.gatling.io/tutorials/scripting-intro-js/#install-gatling)

## Setup Gatling Project Locally 
Once download completed Unzip and open the project in your IDE or terminal and then navigate to the `/javascript` folder for JavaScript projects in your terminal.

You need to run `npm install` to install the packages and dependencies including the `gatling` command. This command will take couple of minute to complete.

I am using VS code, my project sctuture is like below : 


![project structure](/images/posts/gatling-api-load-test-with-javascript/app-structure.png#center)

## Create a Login API Load Test Script

- describing a scenario,
- setting up the injection profile (virtual users profile).

All the test scripts need to be created in side  `javascript/src` folder and feed data (test data) inside `javascript/resources` folder.
### Create Feeder file

Let me create `users.csv` file inside `javascript/resources/` folder and add below data in the file.

```csv
email,password
example1@pb.com,password
example2@pb.com,password
example3@pb.com,password
example4@pb.com,password
```
These users need to be in database and verified. I have registred the users wing `auth/register/` link:

### Importing Gatling functions
To set up the test file use the following procedure:
1. In your IDE create the `login.gatling.js` file in side `javascript/src/` folder.
2. Copy the following import statements and past them in the `login.gatling.js` file.

```js
import {
    simulation,
    scenario,
    exec,
    csv,
    feed,
    repeat,
    rampUsers,
    StringBody,
    jsonPath
} from "@gatling.io/core";
import { http } from "@gatling.io/http";
```
### Define the Simulation function
The simulation function takes the setUp function as an argument, which is used to write a script. To add the simulation function, after the import statements, add:
```js
export default simulation((setUp) => {

});
```
### Configuring the Protocol

Inside the simulation function, define an HTTP protocol. You can define options like `baseUrl`, `acceptHeader`
`acceptLanguageHeader`, `acceptEncodingHeader`, `userAgentHeader` etc.

Here, I am hardcoing `baseUrl` value to `http://localhost:8000` which is the end point for our test app running locally. You can have youir own.

```js
export default simulation((setUp) => {
  // Add the HttpProtocolBuilder:
 const httpProtocol = http
      .baseUrl("http://localhost:8000")
      .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8")
      .acceptLanguageHeader("en-US,en;q=0.5")
      .acceptEncodingHeader("gzip, deflate")
      .userAgentHeader(
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:109.0) Gecko/20100101 Firefox/119.0"
      );
});

```
### Write the scenario 
I have two scenarios: one involves calling the `/auth/login` POST API with `email` and `password` in JSON body. If the login is successful, store the `refreshToken` in a variable named `refreshToken`.

The other scenario is to retrieve the `token` using the `/auth/token` GET API with the  valid `refreshToken` generated by scenarios one.

Both scenarios are repeted 5 times. You can have any valuse depend on your usecase.

```js
export default simulation((setUp) => {

const feeder = csv("users.csv").random();
    const login = exec(
      feed(feeder),
      http("Login")
        .post("/auth/login")
        .body(StringBody('{"email": "#{email}", "password":"#{password}"}')).asJson() 
        .check(
            jsonPath("$.refreshToken").saveAs('refreshToken')
        ) 
    );

    const getAccessToken = exec(
        http("Get Access Token")
          .get("/auth/token/#{refreshToken}").asJson() 
          .check(
            jsonPath("$.token").saveAs('token')
          )
      );

  const httpProtocol = http
      .baseUrl("http://localhost:8000")
      .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8")
      .acceptLanguageHeader("en-US,en;q=0.5")
      .acceptEncodingHeader("gzip, deflate")
      .userAgentHeader(
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:109.0) Gecko/20100101 Firefox/119.0"
      );

    // repeat is a loop resolved at RUNTIME
    const scn = scenario("Login Scenario").repeat(5,'id').on(
      exec(login)
      .pause(1)
      .exec(getAccessToken)
      .pause(1)
    );
});
```
### Define the injection profile

The final step of a Gatling simulation is the injection profile. setUp function need to call exactly once to configure in the injection profile. If you have several scenarios, each needs its own injection profile.

```js
export default simulation((setUp) => {
    const feeder = csv("users.csv").random();
  
    const login = exec(
      feed(feeder),
      http("Login")
        .post("/auth/login")
        .body(StringBody('{"email": "#{email}", "password":"#{password}"}')).asJson() 
        .check(
            jsonPath("$.refreshToken").saveAs('refreshToken')
        ) 
    );

    const getAccessToken = exec(
        http("Get Access Token")
          .get("/auth/token/#{refreshToken}").asJson() 
          .check(
            jsonPath("$.token").saveAs('token')
          )
      );

    const httpProtocol = http
      .baseUrl("http://localhost:8000")
      .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8")
      .acceptLanguageHeader("en-US,en;q=0.5")
      .acceptEncodingHeader("gzip, deflate")
      .userAgentHeader(
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:109.0) Gecko/20100101 Firefox/119.0"
      );
    // repeat is a loop resolved at RUNTIME
    const scn = scenario("Login Scenario").repeat(5,'id').on(
      exec(login)
      .pause(1)
      .exec(getAccessToken)
      .pause(1)
    );

    setUp(
    // 100 concurrent users over 10 seconds
      scn.injectOpen(rampUsers(100).during(10))
    ).protocols(httpProtocol);
  });
```
### Test execution
Now, you should have a completed simulation that looks like the following:

```js
import {
    simulation,
    scenario,
    exec,
    csv,
    feed,
    repeat,
    rampUsers,
    StringBody,
    jsonPath
  } from "@gatling.io/core";
  import { http, status } from "@gatling.io/http";
  
  export default simulation((setUp) => {
    const feeder = csv("users.csv").random();
  
    const login = exec(
      feed(feeder),
      http("Login")
        .post("/auth/login")
        .body(StringBody('{"email": "#{email}", "password":"#{password}"}')).asJson() 
        .check(
            jsonPath("$.refreshToken").saveAs('refreshToken')
        ) 
    );

    const getAccessToken = exec(
        http("Get Access Token")
          .get("/auth/token/#{refreshToken}").asJson() 
          .check(
            jsonPath("$.token").saveAs('token')
          )
      );

    const httpProtocol = http
      .baseUrl("http://localhost:8000")
      .acceptHeader("text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8")
      .acceptLanguageHeader("en-US,en;q=0.5")
      .acceptEncodingHeader("gzip, deflate")
      .userAgentHeader(
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:109.0) Gecko/20100101 Firefox/119.0"
      );
    // repeat is a loop resolved at RUNTIME
    const scn = scenario("Login Scenario").repeat(5,'id').on(
      exec(login)
      .pause(1)
      .exec(getAccessToken)
      .pause(1)
    );

    setUp(
    // 100 concurrent users over 10 seconds
      scn.injectOpen(rampUsers(100).during(10))
    ).protocols(httpProtocol);
  });
```
### Run the Simulation locally
Run following command to run the simulation/test 
```js
npx gatling run --simulation login
```
When the test has finished you will see below result in the termainal.

![simulation output](/images/posts/gatling-api-load-test-with-javascript/simulation-output.png#center)

There is an HTML link in the terminal that you can use to access the static report via browser.

![html report](/images/posts/gatling-api-load-test-with-javascript/html-report.png#center)

Congrats! You have written your first Gatling simulation or APi load test.

