---
title: "Create Q & A With OpenAI ChatGPT in NodeJS and React"
date: 2022-12-15T17:40:07+11:00
draft: false
showToc: true
TocOpen: true
tags : [
"NodeJS",
"ReactJS",
"AI",
]
categories : [
"AI",
"NodeJS",
"ReactJS"
]
keywords: [
"Integrate NodeJS and React",
"Create Q & A With OpenAI ChatGPT in NodeJS and React",
"OpenAI ChatGPT",
"OpenAI",
"Integrate OpenAI",
"Integrate ChatGPT",
"Prakash Bhandari",
]
cover:
   image: "images/posts/create-q-a-with-OpenAI-ChatGPT-in-nodejs-and-react/create-q-a-with-OpenAI-ChatGPT-in-nodejs-and-react.png" 
   alt: "Create Q & A With OpenAI GPTChat in NodeJS and React"
   caption: "Create Q & A With OpenAI GPTChat in NodeJS and React"
   relative: false
author: "Prakash Bhandari"
description: "OpenAI offer a spectrum of models with different levels of power suitable for different tasks,
as well as the ability to fine-tune your own custom models.
These models can be used for everything from content generation to semantic search and classification."
---

OpenAI ChatGPT is trending topics, I heard a lot about it and curious to know more about it. I sign up and tried ChatGPT. 
ChatGPT is really cool, and it's more than what I thought. 

I am using [GitHub Copilot](https://github.com/features/copilot) which is also powered by OpenAI.
OpenAI Codex is a generative pretrained language model which helps people write code even for non-tech people.

ChatGPT dose more than helping people writing code. 
It interacts like human in a conversational way to provide a detailed response to query. 

But, OpenAI CEO, **[Sam Altman](https://twitter.com/sama)** said that "it’s a mistake to rely on it for anything important right now."

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

As a part of this article, I will create a small backend app in NodeJS using OpenAI official `Node.js library` 
and integrate with frontend web app. React will be used to develop frontend app.  
` GPT-3 - text-davinci-003` OpenAI model will be used dialog.

## NodeJS Backend App

We will create a small backend app in NodeJS using `Node.js library`. 
This app will teaks question as input form API and send to the OpenAI GPT model.  

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

### Create package.json file
After installing above mention packages our `package.json` file will be like this.

{{< github-code-snippets 2122f47bc568d5c9087ed5df20531478 >}}

### Crate open.api.service.js file

We will create `open.api.service.js` file inside services folder. This services will connect with the OpenAI system.
`OPENAI_API_KEY` should be stored in the `.env` file. API can be generated form your [OpenAI Account](https://beta.openai.com/account/api-keys)

This file has `getAnswer(question)` function which will accept `question` as input.  Here, we will be using `text-davinci-003` model.

{{< github-code-snippets fae2e207ff1ae1803899eba04750105e >}}

### Create index.js file

App listening on port number `3006`.

We will be creating  `http://localhost:3006/answer` API end point to access via frontend React App. 

From front-end we will be passing questions in `question` query parameter. 
Which will be received via `req.query.question` and it will be send to OpenAI `open.api.service.js`. So,
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
## React Frontend App

We will create a small frontend app in React.
This app will have form which accepts question form user and send to the backend NodeJS app.
NodeJS app will send the answer back to frontend react app, and we will display answer in the UI.

### App files and folders structure
```
| - public
| - src/
| | - Components/
| | | - ChatGPT.js
| | - Services/
| | | ChatGPTService.js
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

### Create  `ChatGPTService.js` Service 

Create  `ChatGPTService.js` service in side `Services` folder. This service will
make an API request to the backend APP that we have written above.

`REACT_APP_API_BASE_URL`  is declared inside `.env` file.

{{< github-code-snippets 0c4fad136a396d4d1cfd54d27e167613 >}}

### Create  `ChatGPT.js` Component

Create  `ChatGPT.js` component inside `Components` folder. This UI component will have input `textarea` and submit button.
You can input the desired question into this textarea and submit. 
Submit action will call the NodeJS backend app API via, which will return the answer of question and display in the UI.
This UI will be simple, I am not going to add fancy CSS 😊. This frontend app has one pitfall, it does not highlight the codes return form ChtaGPT.
`ChatGPTService.js`

{{< github-code-snippets cf15e8b5c14694ccf9e35e4afe5fc227 >}}

### Import `ChatGPT` component in `App` component
`App` component is the main component. In this component we need to import the `ChatGPT` component

{{< github-code-snippets 4dc9db75aeda2f92fe608d4aaad1b728 >}}

### Output

We can browse react app via `http://localhost:3000/`

![OpenAI ChatGPT output](/images/posts/create-q-a-with-OpenAI-ChatGPT-in-nodejs-and-react/output.png#center)

### Demo Video

{{< youtube TwoeHvE7cIE >}}

### Medium Publish Link
https://medium.com/@thebhandariprakash/create-q-a-with-openai-nodejs-and-react-e812933d9cd5

## Public Response to CatGPT 

### Elon musk
{{< tweet user="elonmusk" id="1599128577068650498" >}}


### Health
{{< tweet user="StuartBlitz" id="1602834224284897282" >}}

### Education
{{< tweet user="DanielPink" id="1600611580567293954" >}}
{{< tweet user="ericbahn" id="1598507949638373376" >}}
 



