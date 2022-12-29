---
title: "Is Squash Commits Good Over Regular Merge Commits?"
date: 2022-12-28T13:46:09+11:00
draft: false
showToc: true
TocOpen: true
tags : [
"Git",
]
categories : [
"Git"
]
keywords: [
"Is Squash Commits Good Over Regular Merge Commits?",
"Merge Commits vs Squash Commits",
"Merge Commits and Squash Commits",
"How to Combine Multiple Git Commits into One",
"How do I squash my last N commits together",
"How to Squash All Commits Related to a Single Issue into a Single Commit"
]
cover:
    image: "images/posts/is-squash-commits-good-over-regular-merge-commits/is-squash-commits-good-over-regular-merge-commits.png"
    alt: "Is Squash Commits Good Over Regular Merge Commits?"
    caption: "Is Squash Commits Good Over Regular Merge Commits?"
    relative: false
author: "Prakash Bhandari"
description: "If you are working with git branches you probably know the 'Merge commits' and 'Squash Commits'.
There is philosophical debates between using 'Merge commits' and 'Squash Commits'. But, I found both git commands are very useful based on the situation and use cases."
---

'Merge commits' and 'Squash Commits' both commands has its own advantages and disadvantages. Personally, I use both commands.
I don't have any strong opinion to support only one command as a best command. Both of them are useful for me :).

In this article, I will explain how and when I am using the "Merge commits" and "Squash Commits" based on situation.


## Squash Commits
Sometimes you might be working in a feature which has many commits, and you don't want to maintain the history of all the commits at that time you can use 
"Squash Commits". "Squash Commits" will consolidate all the commits into a final big commit. Squash commits helps you to maintain the clean git history.
Developers should be mindful and wisely while using this command.

We can say "Squash Commits" is alternative to "Merge commits". 
"Squash Commits" retains the changes but omits the individual commits from git history. 
Many developers prefer this workflow in work-in-progress feature branch, all work-in-progress commits aren’t necessarily important for future reference.

It's always good to use "Squash Commits" in work-in-progress branch or addressing reviewers feedback. 
But, sometimes it's dangerous to use "Squash Commits" command, because this command permanently remove the individual important commits which can not be retained in any case.

Let me explain why?

![squash commits](/images/posts/is-squash-commits-good-over-regular-merge-commits/squash-commits.png#center)

In above diagram, commits **D** and **E** from feature branch is combined merged into commit **X** of main branch. 
Retains the changes from commits **D** and **E**  but omits the individual commits from git history. 
Which means, whatever commit information you keep for commits **D** and **E** that will be disappear from git history.

### Will "Squash Commits" maintain clean git history?
Answer is **"YES"**

Sometimes you might need to make a lot of commits because you have many formatting issue or you missed the comment in your code 
or you want to make sure everything is saved before signing out for the day 
or team environment with code reviews, you often see small commits with messages like `fix abc` or `code review feedback`. These are not great commit messages. 
All those commits might not be that useful. Better not to maintain the history of those commits in your git repository.

In this case, it is better to combine all those commits into a final commit so that your commit history will be clean, and you will not have unnecessary commits.
This will simplify your project's Git tree. But, make sure that you will keep detailed commit messages. Unless you have a particular use-case or reason not to.

I recommend to have meaningful commit for a Pull Request(PR) with detail information by explaining what you've done, why you've done it, and what is the expected output.
This information helps other developers to understand the purpose of commit and PR.

### Do we need  "Squash Commits" to maintain clean history?
Answer is **"NO"**

Instead of making small commits for minor changes you can practice following things:

1. Check each and every files before committing
2. Make sure you don't have any formatting issue
3. Make sure that you don't have proper and meaningful comment.
4. Do proper testing before committing 
5. Use a combination of `git cherry-pick` and `git checkout` to combine multiple commits on another branch.

This will help to reduce the number of commits and helps to maintain clean git  history.

### Is "Squash Commits" is always good?
Answer is **"NO"** 

As we discuss above "Squash Commits" is good when you need to combine the commits that are not important or commits which are not essential for future reference.
As for example you don't need to see what are the commits you made for formatting issue.

Combining commits is not always good idea. Sometimes it's dangerous and will loss the important information.
If you use "Squash commits" for the simple reason that will loss the history of the old commits. Which is not good.

As for example let's say your coworker changed a line of code long time ago and used "Squash Commits" to merge with less information. The same changes is causing the issue. 
By inspecting with `git blame` you fond, that changes was made by developer `X` 
and he already left the company. Now, you can not ask him because he already left the company and also, you can not find any information why he made that change.

It would be easy for you if he had used Regular "Merge Commits" or "Squash Commits" with detail information.

I will recommend to think before using the "Squash commits". 

## Regular Merge Commits

Personally, I use "Merge commits" a lot. That does not mean I never use the "Squash Commits". But, I consider the "Merge commits" as a default behaviour of Git.
"Merge commits" preserves all the commit history in chronological order. Those commits are very helpful to track back exactly what happened in each commit.
"Merge commits" is really helpful when you have to identify the commit that cause the particular issue.

"Merge commits" not that much good when you are  working in team environment with code reviews, 
you often make small commits with messages like `fix abc` or `code review feedback` or `formatting corrected` etc. 
These are not great commit messages. These types of commit make developers life harder when developer 
have to identify the actual commit which created an issue.

![Merge commits](/images/posts/is-squash-commits-good-over-regular-merge-commits/git-merge-commits.png#center)

In above diagram, commits **D** and **E** from feature branch is added via commit **X** of main branch.
This will keep both changes and commit information from commits **D** and **E**  
Which means, whatever commit information you added for commits **D** and **E** that will retain from git history by inspecting with `git blame`

## Conclusion

Both "Merge commits" and "Squash Commits" commands has its own advantages and disadvantages. Both has its own use cases. 
Developers should be mindful and these commands need to be wisely chosen before merging the code.
So, that the developers in future will easily understand the commit history. Past commits are the history of project this will tell a story to other developers
"how project was developed". History should be clean and easy to understand.

## References

1. https://www.atlassian.com/git/tutorials/comparing-workflows
2. https://docs.gitlab.com/ee/user/project/merge_requests/squash_and_merge.html

{{< adsbygoogle  ca-pub-6437319845279303 >}}
  


