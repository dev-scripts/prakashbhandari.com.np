---
title: "Parameter vs Argument"
date: 2023-08-02T12:21:58+10:00
tags : [
"Software Development",
]
categories : [
"Software Development",
]
keywords: [
"Parameters vs arguments",
"Parameters",
"arguments",
"In this article, I will try to explain the basic difference between parameter and arguments used in the function."
]
author: "Prakash Bhandari"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
description: "In this article, I will try to explain the basic difference between parameter and arguments used in the function"
disableHLJS: true 
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
    image: "images/posts/parameters-vs-arguments/parameters-vs-arguments.png" 
    alt: "Parameters vs arguments"
    caption: "Parameters vs arguments"
    relative: false
---

# Parameter vs. argument

As a beginner, two terms that often cause confusion to the developers are "parameters" and "arguments". 
As a Joiner Engineer I was interchangeably using the terms parameter and argument. It took me a while to understand. Many of you also using these two term interchangeably.
 
In this article, I will try to explain the basic difference between parameter and arguments used in the function.

## Parameters 
In programming, a parameter is a variable or a placeholder used in a function definition. It is a placeholder for the values that the function will receive when it is being called and hence does not have a concrete value.

### Example

Here's a simple function in C# as an example:

```c#
public int add(int x, int y)
{
    return x + y;
}
```

In `add(int x, int y)` we can see `x` and `y` integer variables, which are parameters.

## Arguments 

Argument is the actual data or a value passed during function invocation.
When you call a function, you provide the required arguments that will fill the placeholders (parameters) 
defined in the function. These arguments can be constants, variables, expressions, or even other function calls.

### Example 
Continuing with our previous example, let's call the `add` function and pass arguments
```c#
add(2, 3);
```
Where, 2 and 3 are arguments

## Working Example

Here is the working example develop in `c#` to add the two numbers. In this example, we have a function `public int add(int x, int y)`
which has int `x` and int `y` as parameters. And the function is invoked inside main method `obj.add(2, 3);` which has `2` and `3` as arguments.

```c#
using System;
					
public class Program
{
	public static void Main()
	{
		Program obj = new Program();
		var total = obj.add(2, 3);
		Console.WriteLine("Total is : "+ total);
	}
	
	public int add(int x, int y)
	{
		return x + y;
	}
}
```

In this article,  we have understood the basic difference between parameter and argument used in the function with example. 
Understanding parameter and argument is also a part of understanding the fundamental concepts of programming.
If you are good at fundamental that take your coding skills to new level!
