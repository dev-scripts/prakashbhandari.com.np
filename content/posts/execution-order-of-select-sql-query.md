+++
title = "Execution Order of SELECT SQL Query"
date="2022-10-11T08:31:35+11:00"
draft=false
categories = [
  "SQL",
]
images = [
  "images/posts/execution-order-of-select-sql-query/execution-order-of-select-sql-query.png"
]
+++


This article is not an unique article. You can find numbers of article related with the **SQL** query execution order. But, I will try to make easy to read and simple to understand.

**SELECT** query is used by many people like Data Engineer, Backend Developer, Fullstack Developer, DBA, and the list goes on.

As we know that the **SELECT** query is used to select data from a database tables. So, the question is what is the order of execution when we execute the **SELECT** query? <!--more-->


Above image clearly explains the execution order of the **SELECT** query. But, I will explain all those 7 steps in few lines.


**1. FROM :-**  Select the set of tables and joins to get data.

**2. WHERE :-**  Applys filter to the records. Only returns those records that fulfill a specified condition. 

**3. GROUP BY :-** Group or aggregate data.

**4. HAVING :-** Is logical predicate to filter the data. WHERE keyword cannot be used with aggregate functions.

**5. SELECT :-** Return the final data.

**6. ORDER BY :-**  Sort data by acending or decending order.

**7. LIMIT :-**  Limit the number of return data.

Sometimes **DISTINCT** is also used after **SELECT** and before **ORDER BY** setps to remove the duplicate records form result and gives unique records.