---
title: "How to Embed GitHub Code Snippets in Hugo Template Blog Posts?"
date: 2022-12-05T14:31:19+11:00
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
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
"How to Embed GitHub Code Snippets in Hugo Template",
"How to Embed GitHub Code Snippets in Hugo Template Blog Posts",
"GitHub Code Snippets",
"Hugo Template",
"How to Embed GitHub Code Snippets",
"How to use GitHub Gists to store and display code snippets in blog posts"
]
tags: [
"Git",
"Hugo Template",
"Gists"
]
categories: [
"GIT",
]
author: "Prakash Bhandari"
cover:
    image: "images/posts/how-to-embed-github-code-snippets-in-hugo-template-blog-posts/how-to-embed-github-gist-code-snippets-in-hugo-template-blog-posts.png"
    alt: "How to Embed GitHub Code Snippets in Hugo Template Blog Posts?"
    caption: "How to Embed GitHub Code Snippets in Hugo Template Blog Posts?"
    relative: true
---

In this post, I am going to show you how to embed GitHub code snippets in the Hugo Template blog post in 5 simple steps.

In **Hugo Template**, we enclose code snippet inside backtick  **```**

Generally, it doesn't highlight the syntax based on what programming language the snippet/code is written in.

**GitHub Gist** code snippets helps us to embed pre-formatted code in markdown.
It's best and simplest way to embed pre-formatted code in markdown. 
It helps us to highlight the syntax based on what programming language the snippet is written in.

YouTube Video:
{{< youtube NOE3E-5ULuA >}}

Here are the 5 simple steps : 

## Step #1: Get your source code

Either you can copy raw code form your GitHub repository or you can directly copy code form your code editor.

## Step #2: Create Secret Gist form https://gist.github.com/
In this step, you need to go to https://gist.github.com/. After browsing the URL, you will see form to input file name and big text box for code. 
Input file name and paste your code in the text box. 

I am copying code form my GitHub and giving fine name `Convert.php` 

![How to Embed Github Code Snippets in Hugo Template](/images/posts/how-to-embed-github-code-snippets-in-hugo-template-blog-posts/gist-github-text-box.png#center)


## Step #3: Get Embed URL From Gist
After you hit the create secret gist. You will see below screen. You can get the embed your form this UI.

```javascript
<script src="https://gist.github.com/dev-scripts/0c318ba491f926abbb194eb62ebeacdf.js"></script>
```

**The above url has two parts :**

1. URl : `https://gist.github.com/dev-scripts/`
2. At the end JS file : `0c318ba491f926abbb194eb62ebeacdf.js`

![gist github embed url](/images/posts/how-to-embed-github-code-snippets-in-hugo-template-blog-posts/gist-github-embed-url.png#center)


## Step #4: Create shortcodes

In your hugo project, inside  `layouts/shortcodes` folder you need to create `shortcodes` file.  My file name will be `github-code-snippets.html`.
In this file you can put below code and I will replace JS file string with `{{ index .Params 0 }}` in the embed script. 
You can do similar in your embed script.

```javascript
<script src="https://gist.github.com/dev-scripts/{{ index .Params 0 }}.js"></script>
```

## Step #5: Embed in Post 

Finally, now you can embed the `shortcodes` for your snippets.

`{{ <github-code-snippets 0c318ba491f926abbb194eb62ebeacdf> }}`

**Above code has two parts :** 

1. Shortcode file name without extension : `github-code-snippets`
2. JS file string form embed script : `0c318ba491f926abbb194eb62ebeacdf` 

Reward for your hard work is here. Now, you can see beautiful code with highlighted syntax. 

Below code snippets with highlighted syntax is the output : 

{{< github-code-snippets 0c318ba491f926abbb194eb62ebeacdf >}}

## References
1. https://gohugo.io/content-management/shortcodes/#gist



