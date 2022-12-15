---
title: "Create Q & A With OpenAI, NodeJS and React"
date: 2022-12-15T17:40:07+11:00
draft: false
showToc: true
TocOpen: true
tags : [
"NodeJS",
"React",
"AI",
]
categories : [
"AI",
]
keywords: [
"Create Q & A With OpenAI, NodeJS and React",
"Create Q & A With OpenAI ChatGPT, NodeJS and React",
"OpenAI ChatGPT",
"OpenAI",
"Integrate OpenAI",
"Integrate ChatGPT",
]
cover:
   image: "images/posts/create-q-a-with-open-ai-nodejs-and-react/create-q-a-with-open-ai-nodejs-and-react.svg" # image path/url
   alt: "Create Q & A With OpenAI, NodeJS and React"
   caption: "Create Q & A With OpenAI, NodeJS and React"
   relative: true
author: "Prakash Bhandari"
description: "OpenAI offer a spectrum of models with different levels of power suitable for different tasks,
as well as the ability to fine-tune your own custom models.
These models can be used for everything from content generation to semantic search and classification."
---

{{< youtube TwoeHvE7cIE >}}
OpenAI is tranding topics, I heard a lot about it and curious to know more about it. I sign up and tried ChatGPT. 
ChatGPT is really cool, and it's more than what I thought.

>OpenAI offer a spectrum of models with different levels of power suitable for different tasks, 
as well as the ability to fine-tune your own custom models. 
These models can be used for everything from content generation to semantic search and classification.

The OpenAI API is powered by a family of models with different capabilities.  

OpenAI Models are : 
1. GPT-3
   - text-davinci-003
   - text-curie-001
   - text-babbage-001
   - text-ada-001
2. Codex
3. Content filter

I went through the API documentation, and it's super easy to understand and integrated.
They have their own official `Node.js library` and `Python bindings` for integration.  

As a part of this article, I will create a small backend app in NodeJS using `Node.js library` 
and integrate with frontend by using React framework. I will use ` GPT-3 - text-davinci-003` model

## NodeJS Backend App
### App files and folders structure

```
|- services/
| | open.api.service.js
| .gitignore
| .env
| index.js
| package-lock.json
| package.json
```
We will be initiating the Node app by running `npm init` command inside the root of the app folder.
This will create `package.json` file. After initiating the app, we will create all other files and folders defined in the project structure. 

We will be installing following packages in our Node App.
1. OpenAI Node.js library `npm install openai --save`
2. Express `npm install express --save`
3. cors  `npm install cors --save`

### package.json file
After installing above mention packages our `package.json` file will be like this.

{{< github-code-snippets 2122f47bc568d5c9087ed5df20531478 >}}

### open.api.service.js file

We will create `open.api.service.js` file inside services folder. This services will connect with the OpenAI system.
`OPENAI_API_KEY` should be stored in the `.env` file.

This file has `getAnswer(question)` function which will accept `question` as input.  Here, we will be using `text-davinci-003` model.

{{< github-code-snippets fae2e207ff1ae1803899eba04750105e >}}

### index.js file

App listening on port number `3006`.

We will be creating  `http://localhost:3006/answer` API end point to access via frontend React App. 

From front-end we will be passing questions in `question` query parameter. 
Which will be recieved via `req.query.question` and it will be send to OpenAI `open.api.service.js`. So,
that we will be getting the desired answer form OpenAI.

{{< github-code-snippets ea03cc644e3ab3fbafc70cf53b4bb72e >}}

Now, our backend app is ready. 

### Run Backend App
We can run this app by running `node index.js` in the root of the app. 

The console should display:

`App listening on port 3006`

If you want to test the API you can try following URL in browser address bar or in postman. This should return some answers.

`http://localhost:3006/answer?question=install nodejs`

## React Frontend App
```
| - public
| - src/
| | - Components/
| | | - ChatGBT.js
| | - Services/
| | | ChatGBTService.js
| .gitignore
| .env
| App.css
| App.js
| index.js
| package-lock.json
| package.json

```
Creating  and running react app is very simple:

1. `npx create-react-app openai-ui`
2. `cd openai-ui`
3. `npm start`

By default, react app will be serving on port number `3000` on localhost when we run the `npm start` command.

`.gitignore`, `.env`, `App.css`, `App.js`, `package.json` and `public/` files and folders are created when we run the `create-react-app`

### Create  `ChatGBTService.js` Service 

Create  `ChatGBTService.js` service in side `Services` folder. This service will
make an API request to the backend APP that we have written above.

`REACT_APP_API_BASE_URL`  is declared inside `.env` file.

{{< github-code-snippets 0c4fad136a396d4d1cfd54d27e167613 >}}

### Create  `ChatGBT.js` Component

Create  `ChatGBT.js` component inside `Components` folder. This UI component will have input `textarea` and submit button.
You can input the desired question into this textarea and submit. 
Submit action will call the NodeJS backend app API via, which will return the answer of question and display in the UI.
`ChatGBTService.js`

{{< github-code-snippets cf15e8b5c14694ccf9e35e4afe5fc227 >}}

### Import   `ChatGBT` component in `App` component
`App` component is the main component. In this component we need to import the `ChatGBT` component

{{< github-code-snippets 4dc9db75aeda2f92fe608d4aaad1b728 >}}

### Output

We can browse react app via `http://localhost:3000/`


![OpenAI ChatGPT output](/images/posts/create-q-a-with-open-ai-nodejs-and-react/output.png#center)