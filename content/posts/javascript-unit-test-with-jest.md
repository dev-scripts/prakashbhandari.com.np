---
title: "Javascript Unit Test With Jest"
date: 2022-11-12T21:56:57+11:00
# weight: 1
# aliases: ["/first"]
tags : [
"NodeJS",
"Unit Test",
]
categories : [
"Javascript",
]
keywords: [
"Unit test with Jest",
"Unit test",
"NodeJS",
]
author: "Prakash Bhandari"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "Javascript Unit Test With Jest"
#canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "images/posts/javascript-unit-test-with-jest/javascript-unit-test-with-jest.png" # image path/url
    alt: "Javascript Unit Test With Jest"
    caption: "Javascript Unit Test With Jest"
    relative: true
---

In this tutorial, I will demonstrate how to write a basic unit test to calculate the area of geometric shapes like circle and rectangle by using the JavaScript Testing Framework called Jest. <!--more-->

Jest can be used for both backend NodeJS and frondend framework like React, Angular, Vue, NestJS, etc.
 
> According to `jestjs.io` "Jest is a delightful JavaScript Testing Framework with a focus on simplicity. 

> It works with projects using: Babel, TypeScript, Node, React, Angular, Vue and more!"
 
I will create a basic NodeJS project to demonstrate the unit test example.
 
Our project folder structure will be like the attached image.
 
![Folder structure](/images/posts/javascript-unit-test-with-jest/project-folder-structure.png#center)
 
 
Let assume you already have installed Node in your machine.
 
Let me create a project folder `js-test`.
```console
mkdir js-test
cd js-test
```
 
`npm init -y`  command needs to be run inside the project folder to create the `package.json` file. Created file will be like below.
 
```json
{
 "name": "js-test",
 "version": "1.0.0",
 "description": "",
 "main": "index.js",
 "scripts": {
   "test": "echo \"Error: no test specified\" && exit 1"
 },
 "keywords": [],
 "author": "",
 "license": "ISC",
 "dependencies": {
   "save-dev": "0.0.1-security"
 }
}
```
 
Next step is to install the `jest` package in our project. So that we can write the unit test. Here, we are installing `jest` as a node module and saving in the `package.json`.
 
```npm
npm i save-dev jest
```
 
After installing the `jest` package in the project you can see the `jset` package in the `package.json` file in the `dependencies` section as below.
 
```json
"dependencies": {
   "jest": "^28.1.3",
   "save-dev": "0.0.1-security"
 }
```
 
Need to configure the `jset` in the `package.json` file. The default text should be replaced by `jest` string in `scripts/test` like below.
 
```json
"scripts": {
   "test": "jest"
 },
```
 
Also for full test coverage we need to pass `--coverage` in that test script.
 
```json
"scripts": {
   "test": "jest --coverage"
 },
```
 
Test setup is completed. Now, we need to write business logics and tests. As above shown folder structure. I will be creating an `area` and `--test` folder inside the `js-test` folder.
 
Inside the `area` folder we will be creating two files `circle.js` and `rectangle.js` and writing logics to calculate the area of geometric shape.
 
Let's create a `circle.js` file as below.
 
```javascript
function area(r) {
 return 3.14 * r * r;
}
 
module.exports = area;
```
 
Let's create a `rectangle.js` file as below.
 
```javascript
function area(l, b) {
 return l * b;
}
 
module.exports = area;
```
 
We have already created the required files and wrote the business logics. Now, it's time to write the actual unit test to test our business logic (area of geometric shape).
 
We will be writing unit tests inside the `__test` folder. Test folder can also have a name like `__test__`.
 
 
Create a file named `rectangle.test.js` and `circle.test.js`. These files will contain our actual test:
 
Inside `circle.test.js`  we will be writing one unit test.
 
```javascript
const area = require("../area/circle");
 
test("The area of a circle is wrong", () => {
 expect(area(2)).toBe(12.56);
});
 
```
 
Inside `rectangle.test.js`  we will be writing two unit tests.
 
```javascript
const area = require("../area/rectangle");
 
test("The area of rectangle is wrong", () => {
 expect(area(2, 3)).toBe(6);
});
 
test("The area of rectangle is wrong", () => {
 expect(area(4, 3)).toBe(12);
});
```
 
We have covered all the business logics and unit tests. Now, It's time to run the test.
 
 
Now we can run `npm test` which will run the unit test as well as create the coverage folder where the meninist file for test coverage is available.
 
![Test result](/images/posts/javascript-unit-test-with-jest/test-results.png#center)

 
Here is the test coverage report. Report is available `coverage/lcov-report/index.html`
 
![Test Coverage](/images/posts/javascript-unit-test-with-jest/test-coverage.png#center)
 
 
In the article, we have learned how to set up a basic Node project, configure `jest` package for unit test and test coverage. Furthermore, we have written some business logics and unit tests for those logics. Also, ran unit tests and generated test coverage reports.

GitHub repository :

```html
git clone https://github.com/dev-scripts/jest-test.git
```
YouTube Video:

{{< youtube cOuboivJK0A >}}
