---
title: "How to Embed a Json File in a .Net Assembly?"
date: 2023-08-20T08:22:58+10:00
draft: false
tags : [
"Software Development",
"dotNet",
"dotNET 7.0",
"dotNET Web Application",
"C Sharp"
]
categories : [
"Software Development",
]
keywords: [
"Embed a Files in a .Net Assembly",
"How to Embed a Json File in a .Net Assembly",
"What is an assembly within the .NET framework?",
"Why sometimes we need to embed files in the assembly?"
]
author: "Prakash Bhandari"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: true
draft: false
hidemeta: false
description: "In this article, I will explain what is assembly and how to include the files within your assembly. So, that you can assess those files in runtime. Such as running unit test to access `.json` file to mock API response."
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
    image: "images/posts/how-to-embed-a-json-file-in-a-net-assembly/how-to-embed-a-json-file-in-a-net-assembly.png"
    alt: "How to Embed a Json File in a .Net Assembly"
    caption: "How to Embed a Json File in a .Net Assembly"
    relative: false
---


Before directly jump into the implementation. It is better to understand what is assembly and why sometimes we need
to embed files like text, json etc. in the assembly.

## What is an assembly within the .NET framework?
As a new .NET programmer, understanding assembly can be very difficult. If you go to Microsoft documentation, you will find the following definition of a web assembly:

>An assembly is a collection of types and resources that are built to work together and form a logical unit of functionality. Assemblies form the fundamental units of deployment, version control, reuse, activation scoping, and security permissions for .NET-based applications


In simple term, an assembly within the .NET framework is essentially a precompiled portion of code that's executable within the .NET runtime environment. 
A .NET application could consist of multiple assemblies. Following diagram will can give a clear idea on what is assembly within the .NET framework.

![How to Embed a Json File in a .Net Assembly](/images/posts/how-to-embed-a-json-file-in-a-net-assembly/how-to-embed-a-json-file-in-a-net-assembly.png#center "How to Embed a Json File in a .Net Assembly")

## Why sometimes we need to embed files in the assembly?

Embedding files like .txt, .json, and .xml into the assembly can offer several advantages. In this article I will be talking about
providing `.json` file as a input to some unit test.

Sometimes we need to input `.json` file in an unit test to mock the actual response that comes form the API. This is a common and effective practice.
This approach helps ensure that your unit tests are isolated, repeatable, and do not depend on external factors such as network availability or API changes. 

Input can be given in many ways, here's how you might do it:

### 1. Create a Mock JSON Response:

Create a `.json` file that contains a mock response you'd expect from the API. This JSON file should represent the structure of the actual API response. 
You can place this file within your unit test project.

### 2. Read the JSON File in Unit Test:
In your unit test method, read the contents of the mock `.json` file and use it to create a mock response object that matches the structure of the expected API response.

### 3. Use the Mocked Response:
Pass the mock response object to the method you're testing as if it came from the actual API. This way, you can test your method's behavior without relying on the real API.

## How to Embed a Json File in a Net Assembly?
Above section we understood what is Assembly and why sometimes we need to embed files in the assembly for unit test.

Now, I will explain how `.json` file can be included in the assembly.

Assuming your project structure is like this:
```javascript
MyProject
|--Abc.Xyz.Tests
|----AbcTest.cs
|----data.json
```

Here's how you can read the data.json file from the same namespace as your test class:

```csharp
using System;
using System.IO;
using System.Reflection;

namespace Abc.Xyz.Tests
{
    public class AbcTest
    {
        public string ReadJsonResource()
        {
            var assembly = Assembly.GetExecutingAssembly();

            var resourceName = $"{GetType().Namespace}.data.json";

            using var stream = assembly.GetManifestResourceStream(resourceName);
            if (stream == null)
            {
                throw new ArgumentException($"Unable to find assembly resource {resourceName}", nameof(resourceName));
            }

            using var reader = new StreamReader(stream);

            return reader.ReadToEnd();
        }
    }
}
```

Both files `AbcTest.cs` and `data.json` files in the same namespace. We need to change the build action of `data.json` file to
`EmbeddedResource` . This is very important step.

Changing the build action of a file to "Embedded Resource" will include the file as an embedded resource within your assembly. 
This will helps you to access the file's content during runtime, such as reading it as a resource within your application code.

In this article, we learned about what is assembly and how to include the files within your assembly. 
So, that you can assess those files in runtime. Such as running unit test to access `.json` file in runtime to mock API response.
