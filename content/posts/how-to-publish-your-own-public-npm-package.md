---
title: "How to Publish Your Own Public NPM Package?"
date: 2022-12-01T12:11:00+11:00
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "This post is about 'How to Publish Your Own Public NPM Package?' To demonstrate, I will create one package to convert Nepali number to English number and English number to Nepali number. Finally, I will publish it to npmjs.com."
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
keywords: [
    "How to Publish Your Own Public NPM Package?", 
    "How to Publish NPM Package ?", 
    "Publish NPM Package", 
    "Nepali to English Number Conversion",
    "English to Nepali Number Conversion"
]
tags: [
"NPM",
"JavaScript",
"NodeJS",
]
categories: [
"NodeJS",
]
author: "Prakash Bhandari"
cover:
    image: "images/posts/how-to-publish-your-own-public-npm-package/how-to-publish-your-own-public-npm-package-to-npmjs.png"
    alt: "How to Publish Your Own Public NPM Package"
    caption: "How to Publish Your Own Public NPM Package?"
    relative: true
---


Publishing your own public NPM package is not that hard. 
All you need is basic understanding of Javascript, NPM, `package.json` file and Account in https://www.npmjs.com

In this post, I will try to explain the steps to create and publish your own public NPM package to https://www.npmjs.com.

To demonstrate, I will create real NPM package which will ***convert English number to Nepali Number and Nepali number to English Number.***
Finally, this package will be publish to https://www.npmjs.com for public use.

## What is NPM?
NPM is a package manager for `Node.js` packages, or modules. 

www.npmjs.com acts like git for `Node.js` packages and hosts millions of public packages which can be downloaded and used in your project.

## What is a Package?
According to [w3schools](https://www.w3schools.com/nodejs/nodejs_npm.asp)
>"A package in Node.js contains all the files you need for a module."

>"Modules are JavaScript libraries you can include in your project."


## NPM Project Setup 
I assume you already have installed the Node in your machine. 
If you don't have node in your machine please install form [Node.js](https://nodejs.org/en/download/).
`NPM` comes with NodeJS, `NPM` gets installed on your machine automatically when you install Node in your machine.

Let's create project folder for our package. 

`mkdir nepali-english-number-converter`

`cd nepali-english-number-converter`

Now, you are in root of the project. Next step is to initiate the project by creating `package.json` file.

`package.json` file gets automatically created  when you run `npm init` command in your project root directory.

Let's create files and folders as defined in below ***Files and Folders Structure for the project***.

## Files and Folders Structure for the project

My file and folder structure will be like below in my project. 
I will be creating all the files and folders as I move further.
```
|- __tests__/
| | english-to-nepali.test.js
| | index.test.js
| | nepali-to-english.test.js
|-src/
| | english-to-nepali.js
| | nepali-to-english.js
| .gitignore
| LICENSE
| package-lock.json
| package.json
| README.md
```
### Create files inside `__tests__/` folder
I will also write unit test by using one of most popular JavaScript framework  called `Jest`.
I have installed Jest for this. But, you can totally ignore this step, because you might feel boring :)

The test code for each file inside the `__tests__/` should look like this. 

1. `english-to-nepali.test.js`

```javascript
const englishToNepali = require("../src/english-to-nepali");

test("english 123 number should converted to १२३", () => {
    expect(englishToNepali(123)).toBe("१२३");
});

test("english 12345 number should converted to १२,३४५", () => {
    expect(englishToNepali(12345)).toBe("१२,३४५");
});

test("english 12345.05 number should converted to १२,३४५.०५", () => {
    expect(englishToNepali(12345.05)).toBe("१२,३४५.०५");
});

test("english 12345.05 number should converted to १२,३४५.०५", () => {
    expect(englishToNepali(12345.05)).toBe("१२,३४५.०५");
});

test("english 123456789.05 number should converted to १२,३४,५६,७८९.०५", () => {
    expect(englishToNepali(123456789.05)).toBe("१२,३४,५६,७८९.०५");
});
```
2. `index.test.js`

```javascript
const convert = require("../src/index");

test("english १२३ number should converted to 123", () => {
  expect(convert("१२३", "TO-EN")).toBe(123);
});

test("english १२३ number should converted to 123 with to-En.", () => {
  expect(convert("१२३", "to-En")).toBe(123);
});

test("english १२३ number should converted to 123 with invalid type. Invalid type should return false", () => {
  expect(convert("१२३", "t1o-En")).toBe(false);
});

test("english १२,३४५ number should converted to 12345", () => {
  expect(convert("१२,३४५", "to-en")).toBe(12345);
});

test("english १२,३४५.०५ number should converted to 12345.05", () => {
  expect(convert("१२,३४५.०५", "TO-EN")).toBe(12345.05);
});

test("english १२,३४,५६,७८९.०५ number should converted to 123456789.05", () => {
  expect(convert("१२,३४,५६,७८९.०५", "tO-eN")).toBe(123456789.05);
});

// english to nepali test
test("english 123 number should converted to १२३", () => {
  expect(convert(123, "TO-NP")).toBe("१२३");
});

test("english 123 number should converted to १२३", () => {
  expect(convert(123, "to-Np")).toBe("१२३");
});

test("english 123 number should converted to १२३ with invalid type. Invalid type should return false", () => {
  expect(convert("१२३", "to-en1")).toBe(false);
});

test("english 12345 number should converted to १२,३४५", () => {
  expect(convert(12345, "to-np")).toBe("१२,३४५");
});

test("english 12345.05 number should converted to १२,३४५.०५", () => {
  expect(convert(12345.05, "to-np")).toBe("१२,३४५.०५");
});

test("english 12345.05 number should converted to १२,३४५.०५", () => {
  expect(convert(12345.05, "To-nP")).toBe("१२,३४५.०५");
});

test("english 123456789.05 number should converted to १२,३४,५६,७८९.०५", () => {
  expect(convert(123456789.05, "tO-np")).toBe("१२,३४,५६,७८९.०५");
});
```
3. `nepali-to-english.test.js`
```javascript
const englishToNepali = require("../src/nepali-to-english");

test("english १२३ number should converted to 123", () => {
    expect(englishToNepali("१२३")).toBe(123);
});

test("english १२,३४५ number should converted to 12345", () => {
    expect(englishToNepali("१२,३४५")).toBe(12345);
});

test("english १२,३४५.०५ number should converted to 12345.05", () => {
    expect(englishToNepali("१२,३४५.०५")).toBe(12345.05);
});

test("english १२,३४,५६,७८९.०५ number should converted to 123456789.05", () => {
    expect(englishToNepali("१२,३४,५६,७८९.०५")).toBe(123456789.05);
});

```

### Create files inside src/ folder
The fines inside `src/` folder should look like this.
1. `english-to-nepali.js`
```javascript
const englishToNepaliNumber = {
    0: "०",
    1: "१",
    2: "२",
    3: "३",
    4: "४",
    5: "५",
    6: "६",
    7: "७",
    8: "८",
    9: "९",
};
const englishToNepali = (englishNumber) => {
    englishNumber = englishNumber.toString().replace(/,/g, "");
    englishNumber = new Intl.NumberFormat("en-IN").format(englishNumber);

    const arrayEnglishNumber = englishNumber
        .toString()
        .split("")
        .map((character) => {
            if (character === "." || character === ",") {
                return character;
            }

            return englishToNepaliNumber[Number(character)];
        });

    return arrayEnglishNumber.join("");
};

module.exports = englishToNepali;

```
2. `index.js`
```javascript
const englishToNepali = require("./english-to-nepali");
const neplaiToEnglish = require("./nepali-to-english");

module.exports = function (number, type) {
  if (typeof type === "undefined") return false;

  if (typeof type !== "string") return false;

  //convert type in upper case
  type = type.toUpperCase();

  if (!["TO-EN", "TO-NP"].includes(type)) return false;

  if (type === "TO-EN") {
    return neplaiToEnglish(number);
  }

  if (type === "TO-NP") {
    return englishToNepali(number);
  }
};
```

3. `nepali-to-english.js`
```javascript
const nepaliToEnglishNumber = {
    "०": 0,
    "१": 1,
    "२": 2,
    "३": 3,
    "४": 4,
    "५": 5,
    "६": 6,
    "७": 7,
    "८": 8,
    "९": 9,
};

const neplaiToEnglish = (nepaliNumber) => {
    nepaliNumber = nepaliNumber.toString().replace(/,/g, "");
    const arrayEnglishNumber = nepaliNumber
        .toString()
        .split("")
        .map((character) => {
            if (character === ".") {
                return character;
            }

            return nepaliToEnglishNumber[character];
        });

    return Number(arrayEnglishNumber.join(""));
};

module.exports = neplaiToEnglish;
```

### Create .gitignore file
You can place all the files and folder that you don't want to commit to git repo.

```.gitignore
node_modules
```

### Create `.npmignore` file
Sometimes you need to also create a `.npmignore` file in your project root folder.
It acts the same way as `.gitignore` does. To ignore the files and folder that you don't want to publish.
For our module we don't need to create.

### Create `LICENSE` file
You can copy and modify my licence text, or you can copy form https://opensource.org/licenses/MIT
### Create `package.json` file

All `npm` packages contain a file, usually in the project root, called `package.json` - 
this file holds various metadata relevant to the project. This file is used to 
give information to `npm` that allows it to identify the project as well as handle 
the project's dependencies.

A `package.json` file will be automatically created when you run `npm init` command in the root of the project.
As mention above, this file holds various metadata like name, version, description, repository url , authors , keywords ect. 

A `package.json` file must contain **"name"** and **"version"** fields.

The **"name"** field contains your package's name, and must be lowercase and one word, and may contain hyphens and underscores.

The **"version"** field must be in the form x.x.x .

If you want to know more about the `package.json` file please visit: [Creating a package.json file](https://docs.npmjs.com/creating-a-package-json-file)

Here, I have attached my `package.json` file. 

```json
{
  "name": "nepali-english-number-converter",
  "version": "1.0.0",
  "description": "English to Nepali and Nepali to English number converter",
  "main": "./src/index.js",
  "repository": "https://github.com/dev-scripts/nepali-english-number-converter.git",
  "scripts": {
    "test": "jest"
  },
  "author": {
    "name": "Prakash Bhandari",
    "email": "thebhandariprakash@gmail.com",
    "url": "https://www.prakashbhandari.com.np"
  },
  "keywords": [
    "neplai number onverter",
    "english to nepali number converter",
    "nepali to english numnber converter",
    "hindu arabic to english numbner",
    "english to hindu arabic",
    "number converter"
  ],
  "bugs": {
    "url": "https://github.com/dev-scripts/nepali-english-number-converter/issues"
  },
  "homepage": "https://github.com/dev-scripts/nepali-english-number-converter#readme",
  "license": "MIT",
  "dependencies": {
    "jest": "^29.3.1",
    "save-dev": "0.0.1-security"
  }
}
```

### Create `README.md` file.

Create `README.md` file, and you can write the document on how to use the package.

## Create Account on `npmjs.com`
If you don't have account on  `npmjs.com`. You have to create an account on https://www.npmjs.com. 
If you have your account on  `npmjs.com` you can publish your module under your account.

## Login into `npmjs.com`
Run `npm login` command in the root of the project. You have to provide the login details from console.

## Publish module to `npmjs.com`

Run `npm publish` inside your project root directory

Once you publish to into `npmjs.com` you are done 🍻 and your package is publicly available to user by the community.

## How to use publicly available NPM package by others people
Now this module is publicly available on `npmjs.com`

 ***Publish URL:***  https://www.npmjs.com/package/nepali-english-number-converter

**Install using `npm `**

```
npm install nepali-english-number-converter --save
```

**Or if you prefer using `yarn`**

```
yarn add nepali-english-number-converter --save
```

Example of package implementation.
```javascript
const convert = require("neplai-number-converter")
console.log(convert(123, 'TO-NP'));

```
Output : `१२३`

After completing this post we have learned how to create a basic 
`NPM` package and publish to the `npmjs.com` for public use.

**Full source code form GitHub Repository** : https://github.com/dev-scripts/nepali-english-number-converter

**Publish URL** :  https://www.npmjs.com/package/nepali-english-number-converter

## References 
1. https://docs.npmjs.com/packages-and-modules/contributing-packages-to-the-registry
2. https://nodejs.org/en/download/
3. https://www.w3schools.com/nodejs/nodejs_npm.asp
