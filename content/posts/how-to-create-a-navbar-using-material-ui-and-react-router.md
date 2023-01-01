---
title: "How to Create a Navigation Bar Using Material UI and React Router?"
date: 2022-12-30T17:54:49+11:00
draft: false
showToc: true
TocOpen: true
tags : [
"React Router",
"Material UI",
"ReactJS",
]
categories : [
"ReactJS",
"JavaScript",
]
keywords: [
"Create a Navbar Using Material UI and React Router",
"How to Create a Navigation Bar Using Material UI and React Router?",
"React Router",
"Material UI",
"Create Navigation Bar in ReactJS",
]
cover:
    image: "images/posts/how-to-create-a-navbar-using-material-ui-and-react-router/how-to-create-a-navbar-using-material-ui-and-react-router-feature-image.png"
    alt: "How to Create a Navbar Using Material UI and React Router"
    caption: "How to Create a Navbar Using Material UI and React Router"
    relative: false
author: "Prakash Bhandari"
description: "Navigation bar or navbar is the very important component of UI design on website. Navigation bar is the collection of
internal hyperlinks which helps users browse through your website pages effortlessly."
---

Navigation bar or navbar is the very important component of UI design on website. Navigation bar is the collection of 
internal hyperlinks which helps users browse through your website pages effortlessly. Design of navbar impact the overall user experience for a website.
There are different types of Navigation bars.
1. Horizontal Navigation Bar
2. Dropdown Navigation Menu
3. Hamburger Navigation Menu
4. Vertical Sidebar Navigation Menu
5. Footer Navigation Menu

Here, I am not going to explain each and every type of the navigation bar. But, I will create a React app with **Horizontal Navigation Bar** by using
**Material UI components and React Router**. Our React app will have three menu item in the top Horizontal Navigation Bar ie. Home, About and Contact.
Each menu item will open its own page.

## Material UI 
**[Material UI  (MUI)](https://mui.com/)** offers a comprehensive suite of UI tools to help you ship new features faster.
## React Router
React Router is a lightweight, fully-featured routing library for the React JavaScript library. 
React Router runs everywhere that React runs; on the web, on the server (using node.js), and on React Native.

## Step 1 : Set-Up React Project
Create a new react app by running the command below in your terminal.

`npx create-react-app nav-bar-in-react`

### Run React App
1. Go inside app root
   Run `cd nav-bar-in-react` command in your terminal.
2. start project
  Run `npm start`command in your terminal.

## Step 2: Install dependencies

### Install Material UI dependency
`npm install @mui/material @emotion/react @emotion/styled --save`

### Install react-router dependency
`npm install react-router-dom --save`

## Step 3: Create Files and Folders Structure for React App

I will be creating all the files, folders and components for the React App.

### Files and Folders Structure for React App
```
| - public
| - src/
| | - Components/
| | | - Navbar.js
| | - Pages/
| | | - About.js
| | | - Contact.js
| | | - Home.js
| .gitignore
| .env
| App.js
| index.js
| package-lock.json
| package.json
```

#### Create `Navbar.js` Component.
`Navbar.js` component will be created inside `src/Components/`. Please check [Material UI](https://mui.com/material-ui/react-app-bar/) 
documentation for your reference.

{{< github-code-snippets f54c04cd1f107ff37d8fc125effe7cd2 >}}

#### Create `About.js`, `Contact.js` and `Home.js` Page Components

`About.js`, `Contact.js` and `Home.js` page components will be created inside `src/pages`. Let me create them one by one.

Crete `About.js` file inside `src/pages`  with some text in body. 

***Example Code***
{{< github-code-snippets 9a08c83385269eecfc3a25f365d57a09 >}}

Crete `Contact.js` file inside `src/pages`  with some text in body.

***Example Code***
{{< github-code-snippets 4cd72303e8febcb24bb6127f7d7c01af >}}

Crete `Home.js` file inside `src/pages`  with some text in body.

***Example Code***

{{< github-code-snippets 5e6882b8f7fc1ecdc77f71952446c614 >}}

## Step 4: Routing and Importing `Navbar` Component

Finally, I will be adding Routing in `App` Component. We don't need to create `App.js` file, it comes with React project setup. Just need to update as per our requirement.

`BrowserRouter`, `Routes`, and `Route` need to be import from `react-router-dom` in `App.js`

Remove old codes from `App` function and add `<Navbar />` component along with all the routers inside `<Routes >` component.

{{< github-code-snippets 5bc5777c27f369dea713d0fc2c875619 >}}

## Demo Output

We can browse React app via `http://localhost:3000/` where you can see the top navigation bar with three menus as shown in below picture.

![Create a Navbar Using Material UI and React Router](/images/posts/how-to-create-a-navbar-using-material-ui-and-react-router/output.png#center)


## YouTube Demo Video.
Here is the demo video of navigation bar uploaded to YouTube

{{< youtube p0_-yhMZZ0I >}}

## GitHub Repository

Here is the GitHub Repository for the navigation bar :- https://github.com/dev-scripts/navigation-bar





