---
title: "Branching Strategy for Big Feature"
date: 2022-11-29T15:50:27+11:00
showToc: true
TocOpen: false
draft: false
hidemeta: false
description: "Branching Strategy for Big Feature"
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
"Branching Strategy for Big Feature",
"Git Flow",
"Working with Git"
]
tags: [
"GIT",
]
categories: [
"GIT",
]
author: "Prakash Bhandari"
cover:
    image: "images/posts/branching-strategy-for-big-feature/branching-strategy-for-big-feature.png"
    alt: "Branching Strategy for Big Feature"
    caption: "Branching Strategy for Big Feature"
    relative: false
---

In a single repository, many developers are developing new features or fixing bugs. 
Some features are small and some of them are big.

Small features can be developed, reviewed, and released quickly. But, big features can not be completed quickly and also it's not a good idea to create a really big pull request (PR).

It's a crazy idea to push the Work In Progress(WIP) feature to the main branch and it may probably get deployed to production. It can be handled in many ways. Like feature flagging, trunk-based development, etc . Sometime I fallow another git strategy for big feature. Generally, I create a main feature branch and create other small branches from the main feature branch. 

In this post, I will not be discussing feature flagging  or trunk-based development. 


In this post, I will be discussing how to create a main feature branch, split the main feature into small features, create small PR for those small features, review, test and merge back to the main feature branch. After completing all the small features, finally, push the whole feature to production. 

I will also try to demonstrate a small practical example.

Let’s say we are going to **integrate the payments system** into our online store. That accepts PayPal, Master Card, Visa, BPay, and Bank Transfers.

If we visualize this piece of work, it's only one payment gateway integration in the online store. This task has many third-party API integrations. 

Let's split the Payment Gateway integration feature into smaller features (we can also call them sub-features)

**Main Feature**
1. Payment Gateway Integration

**Sub Feature**

The main feature can be split into smaller sub feature as below.

1. Paypal integration
2. Mastercard integration
3. Vias integration
4. BPay integration
5. Bank transfer integration  
 
Now we have one main feature `Payment Gateway integration` and five sub features.

Let’s assume we have the following branches in our git flow.

1. ***master***: this is the main branch deployed to the production

2. ***preview***:  release candidate branch or ready to deploy to production or ready to merge to master

3. ***develop***: the main branch for development. The feature ranches can be checkout from develop.


To work on the above feature we need to create a main feature branch from develop

```git
git checkout -b payment-gateway-integration 
```

Now, this branch can act as the main feature branch, and other sub-feature branches can `checkout` `payment-gateway-integration`, create PR, review PR, rebase and merge back to `payment-gateway-integration`. 

You need to push this branch to a remote repository even though there is no commit.  You can add `--allow-empty`  flag in git commit

```git
git commit --allow-empty -m "Payment Gateway integration" 
```

After this, you can work on the Paypal integration feature. This branch needs to be checkout from the main feature branch `payment-gateway-integration` 

```git
git checkout -b paypal-integration
```

Now you can work in this branch and create PR for `payment-gateway-integration` branch. Once the code review is completed for `paypal-integration` you can merge it to `payment-gateway-integration` 

Similarly, you can create separate branches for all the sub-features and merge them back into the main feature branch `payment-gateway-integration`. If more then one developers is working on the sub featues. You or another developer need to rebase his/her working branch form `payment-gateway-integration` depend upon who will first merge their work to main feature brnach (`payment-gateway-integration`).

Once all the sub-feature branches get merged to `payment-gateway-integration` branch you need to `rebase` from develop. Because other developer might have alrady merged their PR to develop and  your brnach is behind those commits.

```git
git checkout payment-gateway-integration
git rebase develop
git push --force-with-lease
```

Finally, you need to create a PR `form payment-gateway-integration` to `develop`. Once you create PR other developers can review it, and QA can test independently in the local feature branch.

This approach helps you to work smoothly without merging Work In Progress(WIP) branch to develop. 






