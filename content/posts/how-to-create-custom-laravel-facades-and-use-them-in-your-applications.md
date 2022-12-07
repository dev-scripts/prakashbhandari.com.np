---
title: "How to Create Custom Laravel Facades And Use Them In Your Laravel Applications?"
date: 2022-12-07T07:32:54+11:00
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
description: "A facade in Laravel is a wrapper around a `non-static` function that turns it into a `static` function."
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
keywords: [
"What Are Laravel Facades and How to Use Them in Laravel Applications",
"How to create custom laravel facades and use them in your applications",
"Create custom laravel facades",
"Laravel facades",
"Build your Laravel facades",
"Laravel 9",
"Build your Laravel facades in Laravel 9",
"How to call non-static function statically in Laravel"
]
tags: [
"Laravel",
"PHP",
]
categories: [
"PHP",
]
cover:
    image: "images/posts/how-to-create-custom-laravel-facades-and-use-them-in-your-applications/how-to-create-custom-laravel-facades-and-use-them-in-your-applications.png" # image path/url
    alt: "How to Create Custom Laravel Facades And Use Them In Your Laravel Applications?"
    caption: "How to Create Custom Laravel Facades And Use Them In Your Laravel Applications?"
    relative: true
author: "Prakash Bhandari"
---

In this post, I will cover what is facades in Laravel, how to create your own custom facades and use them in Laravel applications.

Probably, facades are one of the discussed topics in the laravel community. 

*According to [Dictionary.com](https://www.dictionary.com/browse/facade)*
>The word 'facade' refers to a "superficial appearance or illusion of something."
> In architecture, the term refers to the front of a building.
> Any side of a building facing a public way or space and finished accordingly

In general terms, a facade is the front face or public face of a building. Facade gives a nice outer look of the building by hiding the inner complex structure and makes it easily noticeable.

Similarly, there is a concept of facades in Laravel too. Which is used to wrap a complex library to provide a simpler and more readable interface. 
It makes syntax simple and easily memorable.

*According to Laravel documentation,*
>"Facades provide a "static" interface to classes that are available in the application's service container."

*In simple words,*
>A facade in Laravel is a wrapper around a `non-static` function that turns it into a `static` function.

In Laravel, we have seen many functions that are called statically. These all are examples of built facades.

`DB::get('users')->all();`

`Route::get('/', function () {
});`

`Config::get())`

You can find more built facades in [Laravel Documentation](https://laravel.com/docs/9.x/facades#facade-class-reference)

## Benefit of Using Laravel Facades

Laravel facades serve as `static proxies` to underlying classes in the service container. 
It provides easy and expressive syntax which is more 
maintainable, testable and flexible than traditional static methods.
## Static vs Non-static in PHP 

In definition, we touched the `static` function and `non-static` function. 
Let’s understand what static and non-static functions are in PHP.

### What is a static function in PHP?

Static functions are those functions which do not depend on the instance of the class. In simple words, static functions do not require an instance of the class to execute.

Static methods use double colons `(::)` while accessing properties or methods of a class. 

***Example Code:***

{{< github-code-snippets a22e5b3d6b58d84ed80f9a1491d50fe4 >}}

### What is non-static function in PHP?

Non-static functions are those functions which depend on the instance of the class. In simple words, non-static functions require an instance of the class to execute.

Static methods use arrow operator `(->)` while accessing properties or methods of a class:

`$this` and  class object name  is used to reference properties or methods within a class.

***Example Code:***
{{< github-code-snippets fd47c21b50d185af008a0486218f65d8 >}}

Let me show you the comparison between static and non-static in tabular format.

| No. | Static Function   | Non-static function   |
|:--|:--| :--- |
| 1. | Static functions do not require an instance of the class to execute.  | Non-static functions require an instance of the class to execute.   |
| 2. | Double colons `(::)` while accessing properties or methods of a class| Arrow operator `(->)` while accessing properties or methods of a class|
| 3. | Reserved keywords like `self` , `static` and `parents` are used to reference properties or methods within a class| `$this` is  used to reference properties or methods within a class|

To demonstrate, I will create one class to perform the mathematical operations `add`, `substract`, `multiply` and `divide`
and will wrap this class with facade so that we can call functions of class statically.

## Steps to Create Custom Facades

Before building and implementing your own custom facades. You need to have
a clear picture of the files and folders structure in your mind for your project.

I will be creating following `Calculator` and `Facades` folders with `Calculator.php` and `CalculatorFacade.php` classes.
Moreover, I will create `CalculatorServiceProvider.php` provider class inside existing `Providers` folder.
Finally, I will make required changes in the `config/app.php` and `rounts/web.php` files.

For demo purpose, I will be using **Laravel 9.**

My project folders and files structure.
```
| - app/
| - Calculator/
| | - Calculator.php
| | -Providers/
| | | - CalculatorServiceProvider.php
| | - Facades/
| | | - CalculatorFacade.php
| - config/
| | - app.php
| - rounts/
| | - web.php
```

### Step #1 : Create Main PHP Class File

We will create a`Calculator.php` class which will be wrapped by the facade class. This class will have four `non-static` functions  i.e. `add`, `substract`, `multiply` and `device`
to perform the mathematical operations `add`, `substract`, `multiply` and `device`.
I will create this class in  `App\Calculator` namespace.

***Example Code:***
{{< github-code-snippets 09c6b232b0863bed2f63524695e301c3 >}}

### Step #2 : Create Facade Class
I will create facade class inside `App\Facades` namespace which extends `Illuminate\Support\Facades\Facade`.
The `protected static function getFacadeAccessor()` function will return `calculator` string. 
This string need to be exactly same in the service provider  `register()` function. This string can be anything.

***Example Code:***
{{< github-code-snippets 0f98407d4b47253358ecd92f7e4250dc >}}

### Step #3 : Create Service Provide Class
Create your service provide class which extends `Illuminate\Support\ServiceProvider`. I have created `CalculatorServiceProvider.php` 
service provider class in my providers folder `app/Providers`
Keep in mind this `"calculator"` must be exactly same as return from facades `getFacadeAccessor()` function while binding or wrapping
an instance of your main class `Calculator`

***Example Code:***
{{< github-code-snippets 25c501bdfa747f7e7ee3a8f3eef178a6 >}}

### Step #4 : Register Service Provider Class

Just creating service provider won’t work. You need to register your Service Provider in the `/Config/app.php`
config file, under the `provider` key.

Once you registered, you can now resolve the `Calculator` service from the container by using the global `app()` function.

You can also register Service Provider to `Config\app.php` as aliases. The Aliases can be used reference your class anywhere in your project.

It's optional step.

Here in my project, I am registering`FacadesCalculator`  as alias  for `App\Facades\CalculatorFacade` CalculatorFacade class.
So that you can just call the *alias* and not the whole *Namspaced ClassName*. This alias name be valid string.

If you have registered alias you can use alias to call function like this :  `FacadesCalculator::add(1, 2);`

Otherwise, you need to use whole namespace to call function  `App\Facades\CalculatorFacade::add(1, 2);`

***Example Code:***
{{< github-code-snippets fb0946470ad2cf2e24417f6dc9a0fa30 >}}

### Step #5 : Run `composer dump-autoload`

After finishing all you need to run the `composer dump-autoload` in you project root directory. This command regenerates the list of all classes that need to be included in the project `(autoload_classmap.php)`
It's good to run when you have a new class inside your project. 

### Step #6 : Call `non-static` Functions Statically

You can call functions where ever you need in your project.
Just to demonstrate, I will be calling functions from `web.php` router. So that we can see 
output in the browser screen.

I will call functions statically via facade wrapper as well as non-statically with class's object.

***Example Code:***
{{< github-code-snippets 520adc22d656eece3430d00ba238732e >}}

#### View output
Run `php artisan serve`. This will sever your app in your localhost at port no 8000 (`http://localhost:8000/`)

![How to create custom laravel facades](/images/posts/how-to-create-custom-laravel-facades-and-use-them-in-your-applications/results.png#center)

In this post, I have covered what is facades in Laravel, benefit of using facades, how to create your own custom facades and use them in Laravel applications in very simple steps with example. Also, I have discussed about `static` and `non-static` functions in PHP.

Twitter Post :
{{< tweet user="PrakashsBlog" id="1600608015279075330" >}}
