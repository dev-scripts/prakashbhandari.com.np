---
title: "Strategy Pattern in Node.js: A Practical Approach"
date: 2024-11-14T08:44:29+11:00
showToc: true
TocOpen: true
tags : [
  "Software Design Pattern",
  "JavaScript",
  "NodeJS",
  "Strategy Pattern"
]
categories : [
  "Software Development",
  "NodeJS",
  "Software Engineering"
]
keywords: [
 "Strategy Pattern in Node.js",
 "Strategy Design Pattern",
 "Node.js design patterns",
 "Implementing Strategy Pattern in JavaScript",
 "Behavioral design patterns Node.js",
 "Dynamic algorithm selection Node.js",
 "Node.js payment processing tutorial",
 "SOLID principles in Node.js",
 "Open/Closed Principle examples",
 "Flexible payment methods Node.js",
 "Strategy Design Pattern benefits",
 "Node.js multiple payment gateways",
 "Code maintainability patterns Node.js",
 "Using Strategy Pattern in JavaScript projects",
 "Node.js and SOLID principles explained",
 "Practical examples of Strategy Pattern"
]
cover:
  image: "images/posts/strategy-pattern-in-node-js-a-practical-approach/strategy-pattern.png" 
  alt: "Strategy Pattern in Node.js: A Practical Approach"
  relative: false
author: "Prakash Bhandari"
description: "The Strategy Design Pattern is a behavioral design pattern in software engineering that enables the dynamic selection of algorithms at runtime, allowing for greater flexibility and adaptability in the application’s behavior."
---

In this post, I will explain how to implement the Strategy Design Pattern in Node.js.

To illustrate this concept, I'll use the example of integrating multiple payment methods which can be used in the e-commerse and other applications to accept payment from customers. Whether it's PayPal, bank transfers, or credit card.  

# Strategy Pattern

The Strategy Design Pattern is a behavioral design pattern in software engineering that enables the dynamic selection of algorithms at runtime, allowing for greater flexibility and adaptability in the application's behavior.

This pattern is highly useful for developers in creating flexible, maintainable, and clean code, especially when multiple algorithms are needed to solve a problem.

This pattern promotes the Open/Closed Principle, which states that software entities should be open for extension but closed for modification, as per the SOLID principles.

## Benefits of Using the Strategy Pattern

### Flexibility and Reusability:
Instead of large blocks of if-else statements, the logic is separated into different strategy classes, making the code more flexible, easier to extend, and more readable.

### Separation of Concerns:
Each algorithm has its own dedicated strategy class. All tests and logic can be written for a specific strategy class. Logic change in one strategy will not impact the other strategy. 

### Ease of Testing:
Each strategy class or algorithm can be tested separately, allowing unit tests to be written for each strategy class without affecting the existing functionality of the other strategies.

### Open for Extension
Newer strategy can be easily added  without affecting the existing functionality of the other strategies. 

Although this design pattern has many benefits, it also comes with some drawbacks. For instance, your application may end up with numerous classes, which could lead to confusion and complexity. It’s important to have proper documentation to manage this effectively.

## Components of Strategy Design Pattern
There are four componensts of the strategy desing pattern 
1. **Context :** The Context is the main class that uses a strategy to perform tasks. It allows switching algorithms without modifying the existing code. In our case below piece of code is the context.
```js
 const strategies = {
            payPal: new PayPalStrategy(),
            bankTransfer: new BankTransferStrategy(),
            creditCard: new CreditCardStrategy(),
            //add more payment Strategy if needed ....
        };
```
2. **Strategy Interface :** Defines common methods that all strategies must implement. In JavaScript, interfaces cannot be defined directly, but in TypeScript, they can be used to achieve this.

3. **Concrete Strategy :** Actual implementation of the algorithm. In our case `./BankTransferStrategy.js`;
`PayPalStrategy.js` and `CreditCardStrategy.js` are the Concrete Strategy.

4. **Client :** Interacts with the Context and triggers the use of a strategy. In our case clint would be API end point `http://localhost:3000/pay`

# Strategy Pattern in Node.js

Let's assume you already have installed Node in your machine.

## Initialize a Node.js project
Let me create a project folder `nodejs-strategy-pattern`. You can follow below setps to initate the node.js app.
```console
mkdir nodejs-strategy-pattern
cd nodejs-strategy-pattern
```
 
 ## Install dependencies
`npm init -y`  command needs to be run inside the project folder to create the `package.json` file. 
 
Next step is to install the `express` package in our project. Here, we are installing `express` as a node module and saving in the `package.json`.
 
```npm
npm i express -save
```

 Also add 

 ` "start": "node app.js",` in `scripts`

After installing the `express` package your `package.json` file will be like below.
 
```json
{
  "name": "nodejs-strategy-pattern",
  "version": "1.0.0",
  "main": "app.js",
  "type": "module",
  "scripts": {
    "start": "node app.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "description": "",
  "dependencies": {
    "express": "^4.21.2"
  }
}
```

## Create the necessary files:
Create necessary strategy class and fiels that are required. We have the following structure:

```js
nodejs-strategy-pattern/
├── app.js            # Main application file
├── BankTransferStrategy.js  # Bank transfer payment strategy
├── PayPalStrategy.js      # PayPal payment strategy
├── CreditCardStrategy.js     # Credit Card payment strategy , add more strategy if you need
└── package.json

```
## Creating Payment Strategies
We define three classes (`PayPalStrategy`, `BankTransferStrategy`, and `CreditCardStrategy`) that encapsulate the logic for processing payments through their respective providers.

###  BankTransferStrategy class
Create a BankTransferStrategy class that simulates processing a payment through Bank Transfer.

```js
export default class BankTransferStrategy {
    async pay(amount) {
        return {
            message: `${amount} paid via Bank Transfer.`
        };
    }
}
```

###  PayPalStrategy class
Next, create a PayPalStrategy class that simulates processing a payment through PayPal.

```js
export default class PayPalStrategy {
    async pay(amount) {
        return {
            message: `${amount} paid via PayPal.`
        };
    }
}
```

###  StripeStrategy class
Finally, create a StripeStrategy class that simulates processing a payment through Stripe.

```js
export default class CreditCardStrategy {
    async pay(amount) {
        return {
            message: `${amount} paid via Credit Card.`
        };
    }
}
```

## Implementing the Payment Handling Logic in app.js

Now that we have our strategies in place, let's move on to the main application file (`app.js`), where we will use the Strategy Pattern to select the correct payment provider based on user input.

```js
import express from 'express'; /
import BankTransferStrategy from './BankTransferStrategy.js';
import PayPalStrategy from './PayPalStrategy.js';
import CreditCardStrategy from './CreditCardStrategy.js';
// import more payment Strategy if needed ....

const app = express();
const PORT = 3000;// Run app in the port 3000

// Middleware to parse JSON request bodies
app.use(express.json());

// Handle POST requests (4. client)
app.post('/pay', async (req, res) => {
    try {
        const { provider, amount } = req.body; // Get data from request body
        console.log('Received data:', req.body);

        // 1. Context
        // Define payment strategies
        //The correct payment strategy is selected from the strategies object based on the provider specified in the request body.
        const strategies = {
            payPal: new PayPalStrategy(),
            bankTransfer: new BankTransferStrategy(),
            creditCard: new CreditCardStrategy(),
            //add more payment Strategy if needed ....
        };

        // Select the payment strategy based on the provider
        const paymentStrategy = strategies[provider];


        if (!paymentStrategy) {
            return res.status(400).json({ 
                message: "Invalid provider: Provider type should be either 'payPal', 'bankTransfer', or 'creditCard' (case-sensitive)." 
            });
        }        

        // Process the payment
        const result = await paymentStrategy.pay(amount);

        // Send success response
        return res.status(200).json({ message: result });
    } catch (err) {
        console.error('Error processing payment:', err.message);

        // Send error response
        return res.status(500).json({ message: 'Unexpected error occurred.' });
    }
});

// Start the server on port no 3000
app.listen(PORT, () => {
    console.log(`Server is running on http://localhost:${PORT}`);
});
 
```

Now, it’s time to test the application we built. You can run
`node app.js` or `npm start` to start the app on port 3000.

Since our app is running on localhost at port 3000, we can submit JSON payload  specifying the payment provider and amount to the API endpoint `http://localhost:3000/pay`. Based on the provider, the application dynamically chooses the corresponding strategy to process the payment.

Here is the example of provider using `payPal`

**Sample Payload:**

```json
{
    "provider" : "payPal", // 'payPal', 'bankTransfer', or 'creditCard'
    "amount" : 10 // amount can be any numeric value.
}
```

**Sample Resposne:**
```json
{
    "message": {
        "message": "10 paid via PayPal."
    }
}
```

Here is the full screenshot of the request and response:

![Strategy Pattern in Node.js](/images/posts/strategy-pattern-in-node-js-a-practical-approach/postman-test.png#center "Strategy Pattern in Node.js")

Instead of `payPal`, you can easily switch the payment provider to `bankTransfer` or `creditCard`, and it will automatically use the correct `PaymentStrategy` class.

You can even add a new payment provider, such as `bitcoin`. To do this, simply create a new strategy class to handle Bitcoin payments and add context.

## conclusion
In conclusion, the Strategy Pattern is a powerful design approach that enhances flexibility, maintainability, and adherence to SOLID principles. By decoupling algorithms into distinct strategy classes, it allows for easy extension of functionality without altering existing code. This makes it particularly well-suited for scenarios like dynamic payment processing, where seamless integration of multiple options is required.

In this post, we also explored a practical implementation of the Strategy Pattern by integrating payment providers into a Node.js application.