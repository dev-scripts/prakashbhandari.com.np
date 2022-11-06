+++
author = "Prakash Bhandari"
title = "Code Review in Software Development Process"
date = "2020-08-06T11:00:52+11:00"
description = "Code Review in Software Development Process"
draft = false
tags = [
    "Code Review",
    "Git",
]
categories = [
  "Code Review",
  "Git",
]
images = [
  "images/posts/code-review-process-in-software-development/code-review-process.png"
]
+++

In the Software Development process, code review is one of the major processes. It plays a vital role in developing quality software and reducing human errors. This article highlights the significance of code review and tries to answer <!--more-->questions related to the code review process like Who will be involved in this process, Whose code should be reviewed, What reviewer will look at code, Where code review is done and is spending time on code review worthwhile?

## What is Code review?

Code review is also referred to as peer review. It is a part of the software quality assurance process which involves examining the source code to identify bugs at an early stage. A code review process is typically conducted before merging to the mainline branch.

The main purpose of the code review process is to maintain the code quality (Readable, Maintainable, and Extensible) and write bug-free software programs.

## Who is involved in a code review?

In the code review process,  at least two roles are always present in a team.  A developer who writes code and creates Pull Request (PR)  is referred as  an “author” and the developer(s) who examines the quality of code is referred as “Reviewers”.   The role of the reviewer and the author can be elaborated in more depth with the following diagram:


## When code review should be done?

Let's raise a few more questions here before answering this question.

Do we need to review the code of developers who have a few years of experience (1- 5 years)?
Do we need to review the code of developers who have several years of experience (5+ years)?
Do we need to review the code of Software Architect?
The answer is “Yes”, we need to review the code of all the developers regardless of their experience and programming skills. They all are human beings, they are not perfect and will make mistakes. Huang (2016, pp. 108-113) has done study and his results show that 23 out of 55 (41.8%) developers committed the error of ‘forgetting to print a blank line after each word’.

## When code review should be done?
Actual code reviews should be done after all the automated checks are performed and Right before the working branch is merged with the main branch (Source code).  According to Fowler 2020, mainline branch is a special branch  that acts as the current state of a project's code base.

## Where code review should be done?
A review can be done in many places like on developers’ machines, online platforms, etc. The below points clarifies where and how code review is done.

1. Over-the-shoulder : Code reviews are done on the developer’s workstation. It is only possible if the reviewer can walk to the author's workstation.

2. Email pass-around : The author (or through Version Control Systems) emails code to reviewers. This is the traditional way of reviewing code, but this tends to be no longer followed by the developers.

3. Pair Programming : In this method, more than one developer can be found at a workstation, but only one of them writes the code whereas the other provides real-time feedback and suggestions.

 4. Tool-assisted : Authors and reviewers use specialized tools designed for peer code review. The tool could be open source or paid like GitHub, BitBucket, etc. It is the popular and preferred method of reviewing codes in the current era.

## What Do Code Reviewers Look For?
According to Google's Engineering Practices documentation, code reviews should look at the following things:

1. Design: Is the code well-designed, appropriate, and efficient?
Functionality: Does code meet the requirements and behave as the author likely intended? 
2. Complexity: code is easy to read, understand, and use? Could the code be made simpler?
3. Tests: Does the code cover automated tests for each function?
4. Name: Are names for variables, classes, methods, etc. clear and followed proper naming conventions?
5. Comments: Are the comments clear,  useful, and at a proper place?
6. Style: Does the code follow proper coding standards or guidelines
7. Documentation: Does have proper documentation for API?

## What are the benefits of code review?

Code review  provides a large number of benefits for a company dealing with code development as well as software developers.  It’s advantages are extended beyond  finding errors and fixing them.  Let's dive deep into the advantages of code review,

![benifit of code review](images/posts/code-review-process-in-software-development/benifit-of-code-review.png#center)

1. ### Knowledge Sharing 
In a perfect world, the author will create the pull request (PR) (on bitbucket/GitHub) for code that needs to be reviewed and reviewers will go through each line of code. This process encourages the sharing of ideas, experiences, and knowledge across the team where both authors and reviewers can learn from each other.

Also, Code Review helps people to learn about new logic, concepts and methods of writing a code which they might not have experienced before.

### Train new Developers
The code review process provides an opportunity for new developers to learn from experienced developers and improve their coding skills.

### Consistent Development Process
Code review helps to maintain consistency in design and implementation throughout a software project, which will improve the readability of the code. To maintain the consistency reviewers can enforce the author to follow the proper coding standard, design patterns/principles, naming conventions (for variable names and database column names), etc. In the long run, this helps to improve the code quality, saves time and resources. It is an essential factor for the large codebase.

### Team cohesion
Code review always brings the development team together and closer. Also, it helps them to understand each other's technical skills.

### Higher Quantity Code and cost reduction
Code reviews help to maintain a higher quality code. This process helps to identify the bugs and security issues at an early stage, which positively impacts the time  spent in handling technical debt (also known as design debt or code debt, but can be also related to other technical endeavors) after release. Technical debt is the cost a software company has to pay because of poor development processes within its existing codebase. These debts should be resolved as quickly as possible otherwise a company has to bear a big cost.

According to Codacy "Software developers spend more than 26% of their time working on code debt and on bug fixing."

## Do code reviews take the time or save time?

According to Atlassian agile coach documentation "When done right, code reviews save time in the long run." This clarifies that code review is not a waste of time.

In summary,  the code review process plays a vital role to write a quality code and develop bug-free software. It has tons of benefits for both organizations as well as developers.  Moreover, this process minimizes cost, saves time, reduces the risk, and brings the development team together for better planning and quality work.

Happy reviewing!


## Refrences

1. Fowler, M 2020, Patterns for Managing Source Code Branches, viewed 6 September 2020, https://martinfowler.com/articles/branching-patterns.html 
2. Google Inc. 2020, Google's Engineering Practices documentation, Google Inc, viewed 5 September 2020, https://google.github.io/eng-practices/review/
3. F. Huang, "Post-Completion Error in Software Development," 2016 IEEE/ACM Cooperative and Human Aspects of Software Engineering (CHASE), Austin, TX, 2016, pp. 108-113, doi: 10.1109/CHASE.2016.030.
4. Jamie 2018, How To Reduce Technical Debt Using Code Review Tools, Codacy  viewed 5 September 2020  https://blog.codacy.com/how-to-reduce-technical-debt-by-using-code-review-tools/   
5. Radigan, D 2020, Why code reviews matter (and actually save time!), Atlassian, viewed 5 September 2020, https://www.atlassian.com/agile/software-development/code-reviews