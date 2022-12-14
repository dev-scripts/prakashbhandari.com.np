---
title: "What Are Return Types in PHP ?"
date: 2022-12-11T12:05:07+11:00
draft: false
showToc: true
TocOpen: true
tags : [
"PHP",
]
categories : [
"Javascript",
]
keywords: [
"What Are Return Types in PHP ?",
"int return type in PHP",
"float return type in PHP",
"string return type in PHP",
"interfaces return type in PHP",
"array return type in PHP",
"callable return type in PHP",
"iterable return type in PHP",
"nullable return type in PHP",
"void return type in PHP",
"object Return type in PHP",
"Union return type in PHP",
"return only type static in PHP",
"mixed return type in PHP",
"never return type in PHP",
"intersection types in PHP",
"intersection types in PHP",
"intersection types in PHP",
"intersection types in PHP",
"types null and false standalone types in PHP",
"literal type true in PHP",
"DNF types in PHP"
]
cover:
    image: "images/posts/what-are-return-types-in-php/what-are-return-types-in-php.png" # image path/url
    alt: "What Are Return Types in PHP ?"
    caption: "What Are Return Types in PHP ?"
    relative: true
author: "Prakash Bhandari"
description: "PHP 7 followed on the heels of PHP 5.6 and the release was a big revolution. Chronologically PHP 7 is confusing version but brought enormous improvements in PHP engine performance.
It also introduced a variety of new, impactful features that made it quickly adopted."
---

I studied `Java` in my university. It was strictly typed programming language and had to define the 
return type for a function.
But, I started my career as PHP developer with PHP programming. 
`PHP 5.6` did not had a concept of return type or type hinting. 
In my early days, I used to get confused.

I worked in many small to big projects. For all projects
I used PHP and this language become primary backend programming language for me.

Now, I am also working with other languages. But, I have deep connection with PHP.
I am closely monitoring the evolution in PHP space.



PHP 7 followed on the heels of PHP 5.6 and the release was a big revolution. 

Chronologically PHP 7 is confusing version but brought enormous improvements in PHP engine performance.
It also introduced a variety of new, impactful features that made it quickly adopted.

In this release PHP community added a new impactful feature **Type hinting**. Type hinting is being referred to as **Return Type declaration**.
Return type declaration specifies the type of value that a function should return.
Also, community releasing new Type Hinting in new releases.

In this post, I will try to explain **Type hinting** added in each release with example.

## PHP 7.0.0
`PHP 7.0.0` added new features to support `int` `float`, `bool`, `string`, `interfaces`, `array` and `callable` return types.
I will explain them with examples.
### 1. `int` return type

`int` return type declaration specifies that a function can return integer value. 

**Example:**
```php
declare(strict_types=1);

function add(int $a, int $b) : int {
 return $a + $b;
}

$m = add ( 2 , 3 ); // 5
```
### 2. `float` return type

`float` return type declaration specifies that a function can return floating number ie `1.2`, `0.5`, etc.

**Example:**
```php
function divide(int $a, int $b) : float {
 return $a / $b;
}

$m = divide( 3 , 2 ); // 1.5
```
### 3. `bool` return type
`bool` return type declaration specifies that a function should return boolean value fo `true` or `false`

**Example:**
```php
function isPHP5(string $version) : bool {
 return ($version=='PHP5') ? true :false;
}
$m = isPHP5('PHP5'); // true
```
### 4. `string` return type

`string` return type declaration specifies that a function should return boolean value of string. It could be combination of alphabets, numbers and special characters.

**Example:**
```php
<?php
function getPHPVersion(string $version) : string {
 return $version;
}
$m = getPHPVersion ( 'PHP5' ); // PHP5
```

### 5. `interfaces` return type

`interfaces` return type declaration specifies that a function should return interfaces rather than a specific value.

**Example:**
```php
<?php 
interface PaymentMethodInterface {
  public function pay(): string;  
}

class MasterCard implements PaymentMethodInterface {

  public function pay(): string  {
    return "Paid by MasterCard";
  }
}

class Paypal implements PaymentMethodInterface {
  public function pay() {
    return "Paid by Paypal";
  }
}

class Factory {
  public function create($method): PaymentMethodInterface  {
    if ($method == "MasterCard") {
      return new MasterCard();
    } else if ($method == "Paypal") {
      return new Paypal();
    }
  }
}

$factory = new Factory();

$paypal =  $factory->create('Paypal');
echo $paypal->pay();//  output : Paid by Paypal

$masterCard =  $factory->create('MasterCard');
echo $masterCard->pay();// output:  Paid by MasterCard
```

### 6. `array` return type
`array` return type declaration specifies that a function should return array value. 

**Example:**
```php
<?php
function getPHPVersions() : array {
 return ["PHP 5.6", "PHP 7.x", "PHP 8.x"];
}

getPHPVersions(); 
```
### 7. `callable` return type
Type declarations can be added to function arguments, return values.
```php
<?php
function callableExample(callable $callableExample) : callable {
    $a = $callableExample();
    return function ($b) use ($a) {return $a + $b;};
}

//Or
function callableExample(callable $callableExample : callable {
    $a = $callableExample();
    return fn($b) => $a + $b;
}
```

## PHP 7.1.0
`PHP 7.1.0` added new features to support `iterable` `void`,  and `null` return types.

### 1. `iterable` return type
`iterable` is a built-in compile time type alias for array|Traversable. 

**Example:**
```php
<?php
function gen(): iterable {
    yield 1;
    yield 2;
    yield 3;
}
?>
```

### 2. `void` return type

`void` return type is used in a function if function dose not return anything. 

**Example**
```php
<?php
class VoidTestClass {
    public function test() : void {
    echo "I am void"
    }
}

$voidTestClass = new VoidTestClass();
$voidTestClass->test(); // Output : I am void 
```

### 3. `nullable` return type

`null` can be passed as an argument, or returned as a value, respectively.

**Example:**
```php
<?php
  public function getLanguage( ? $language): ?string
  {
    if ($language === 'PHP') {
      return $language;
    }
  
    return null;
  }
 
  echo getLanguage("PHP")// Output : PHP
  echo getLanguage(null)// Output : null
```

## PHP 7.2.0
`PHP 7.2.0` added new features to support `object` return type for a function.
### 1. `object` Return type

`Object` is used to  return an instances of any class. In our example, we are declaring `Object` as return type for `returnA()` in class `B`
```php
<?php
class A {
  function getText(string $text) : string
  {
    return $text;
  }
}

class B {
  function returnA() : Object //In older version of PHP we have to return  A
   {
     return new A();
   }
}
$objB = new B();
$objA = $objB->returnA();

echo $objA->getText("getText function is called.");
//output : getText function is called.
?>
```

## PHP 8.0.0

`PHP 8.0.0` added new features to support `Union return type`,  only `static` and `mixed` return type for a function.

### 1. Union return type
`PHP 7.1.0` have `nullable` types, which means you can declare the type to be `null` with a type declaration similar to `?string`

From `PHP 8.0`, you can declare more than one type for arguments, return types, and class properties.

**Example:**
```php
<?php
public function add(float|int $num1, float|int $num2): int|float {
        return $num1 + $num2;
    }
    
echo add(2, 1) // output : 3
echo add(2.5, 1) // output : 3.5
echo add(2.5, 1.5) // output : 4
echo add(2, 1.5) // output : 3.5
```

### 2. return only type `static`
PHP class methods can return `self` and parent in older versions, but `static` was not allowed.
`PHP 8.0` allowed `static` return type.

**Example**
```php
class ClassName {
    public static function getInstance(): static {
        return new static();
    }
}
```
### 3. `mixed` return type

`mixed` return type is equivalent to a Union Type of:
`string|int|float|bool|null|array|object|callable|resource`

**Example**
```php
class ClassName {
    public  function getInstance(): mixed {
        return "php";
    }
}
```

## PHP 8.1.0

`PHP 8.1.0` added new features to support `never` and `intersection` types.  
Also, **returning by reference from a void function was deprecated**
### 1. `never` return type

The function that always throws an exception or terminates with a die/exit call can be declared with the `never`. 
A  `never` return type indicates that it will never return a value,

It's similar to the  `void` return type, but the `never`  return type guarantees that the program will throw and exception or terminate.

**Example**
```php
<?php
function redirect(string $url): never {
    header('Location: ' . $url);
    exit();
}
```
### 2. `intersection` types

`intersection` types let you typehint values that must satisfy more than one type constraint.
From `PHP 8.1.0` we already have union types that combine types with a logical `“or”` clause; `intersection` types offer an logical `“and”` clause instead.
We already know that "the type of `$value` should be `A` or `B`":
```php
<?php
function test(A|B $value) { 
/* … */ 
}
```

As we discussed above `intersection` types offer an logical `“and”` clause. The `$value` type should be `A` and `B`:
```php
<?php
function test(A&B $value) { 
/* … */ 
}
```
This type hint is not frequently used like other type hints.

## PHP 8.2.0
`PHP 8.2.0` added new features to support literal type `true`, `DNF` types and `null` and `false` can be used standalone.

**Example**

### 1. The types `null` and `false` can be used standalone.
PHP 8.2.0 started to support `null` and `false` as standalone types. 
In previous, versions, we can not use these types like standalone type.

**Example**
```php
function getUsers(): null {}
function isLogin(): false {}
```

But can not do like this. This will throw error
```php
function getUsers(): ?null {}
```

### 2. literal type `true`
PHP 8.2.0 started to support `true`  types, `DNF` types and 
`true` type can be used as part of `Union Types`, But you can not use it with `bool` it creates ambiguity
```php
function isLogin(true|string): true {
    return true;
}

isLogin(true);
```
### 3. `DNF` types

`PHP 8.2.0` started to support `DNF` type by combining Union Types available form `PHP 8.0` and Intersection Types available form  `PHP 8.1`. 
One of the most common use cases is to declare a type that accepts an Intersection type, or null (i.e. nullable intersection types). 
which means they can become nullable: `(A&B)|null`

**Example**
```php
class DNFExample {
    public function DnfFunction((A&B)|null $entity) {
        if ($entity === null) {
            return null;
        }
 
        return $entity;
    }
}

```



## Refrances 
1. https://www.php.net/manual/en/language.types.declarations.php

