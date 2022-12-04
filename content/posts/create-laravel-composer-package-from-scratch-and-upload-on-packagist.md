---
title: "Create Laravel Composer Package From Scratch and Publish on Packagist"
date: 2022-12-04T09:50:12+11:00
showToc: true
TocOpen: true
draft: false
hidemeta: false
comments: false
description: "This post is about 'Create Laravel Composer Package From Scratch and Publish on Packagist' To demonstrate, I will create one Laravel package to convert Hindu Arabic(Nepali)
number to English and English number to Hindu Arabic (Nepali) number. Finally, I will publish this package to https://packagist.org/."
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
"How to Publish Your Own Public Laravel Package?",
"How to Publish PHP Package ?",
"Publish PHP Package",
"Nepali to English Number Converter",
"English to Nepali Number Converter"
]
tags: [
"PHP",
"Laravel",
"Package",
]
categories: [
"PHP",
]
author: "Prakash Bhandari"
cover:
    image: "images/posts/create-laravel-composer-package-from-scratch-and-publish-on-packagist/create-laravel-composer-package-from-scratch-and-publish-on-packagist-org.png"
    alt: "Create Laravel Composer Package From Scratch and Publish on Packagist"
    caption: "Create Laravel Composer Package From Scratch and Publish on Packagist"
    relative: true
---


In this post, I’m going to explain how you can create Laravel package locally and publish to [Packagist](https://packagist.org/).

In this post, I will create a package to convert Hindu Arabic(Nepali) number to English and  English number to Hindu Arabic (Nepali).

Creating Laravel package is not that hard.

In few simple steps you can create your own package. I will go through few steps:

## Step #1 : Install Laravel

I choose Laravel 9 because Laravel 9 is the latest version of Laravel. In future, you will have newer version of Laravel.

One simple command can install Laravel project in your machine. But,  it has dependency on composer.

I am assuming you already have installed composer in your machine.

If you have not installed composer please install [Composer](https://getcomposer.org/download/)

Install Laravel in your machine by running below command.

```
composer create-project --prefer-dist laravel/laravel example-app
```

## Step #2: Folder Structure

Files and folders structure will be like below inside my `example-app` project.

```
| - app/
| - bootstrap/
| - config/
| - database/
| - lang/
| - packages/
| | - dev-scripts/
| | | - english-nepali-number-converter-php/
| | | | - src/
| | | | | - Convert.php
| | | | | - ConvertToEnglish.php
| | | | | - ConvertToNepali.php
| | | | | - EnglishNepaliNumberConverterProvider.php
| | | | - tests/
| | | | | - Unit/
| | | | | | - ConvertTest.php
| | | | | - UnitTestCase.php
| | | | - .gitignore
| | | | - composer.json
| | | | - composer.lock
| | | | - README.md
| - public/
| - resources/
| - rounts/
| - storeage/
| - tests/
| - vendor/
| - .env
| - .gitignore
| - composer.lock
| - composer.json
| - phpunit.xml
| - README.md
```

## Step #3 : Create Folder and Files inside `packages` folder

Go inside `english-nepali-number-converter-php` folder from console and run :
```
composer init
```

Above command creates `composer.json` file inside `packages/dev-script/english-nepali-number-converter-php`

After crating `composer.json` file. You can create all the folders and files inside package folder. The folder structure should look like:
```
| - packages/
| | - dev-scripts/
| | | - english-nepali-number-converter-php/
| | | | - src/
| | | | | - Convert.php
| | | | | - ConvertToEnglish.php
| | | | | - ConvertToNepali.php
| | | | | - EnglishNepaliNumberConverterProvider.php
| | | | - tests/
| | | | | - Unit/
| | | | | | - ConvertTest.php
| | | | | - UnitTestCase.php
| | | | - .gitignore
| | | | - composer.json
| | | | - composer.lock
| | | | - README.md
```

Your `composer.json` file will only have basic configuration. You need to update your `composer.json` file with additional configurations.

### Updating `composer.json` file
```json
{
  "name": "dev-scripts/english-nepali-number-converter-php",
  "description": "Package to convert English number to Nepali number and Nepali number to English number.",
  "keywords": [
    "Nepali Number converter",
    "English to Nepali number converter",
    "Nepali to English number converter",
    "Hindu Arabic to English number",
    "English number to Hindu Arabic",
    "Number Converter",
    "PHP package converter English number to Nepali number",
    "PHP package converter Nepali number to English number"
  ],
  "minimum-stability": "dev",
  "type": "library",
  "license": "MIT",
  "authors": [
    {
      "name": "Prakash Bhandari",
      "email": "thebhandariprakash@gmail.com"
    }
  ],
  "homepage": "https://prakashbhandari.com.np/",
  "support": {
    "issues": "https://github.com/dev-scripts/english-nepali-number-converter-php/issues"
  },
  "autoload": {
    "psr-4": {
      "DevScripts\\EnglishNepaliNumberConverter\\": "src/"
    }
  },
  "autoload-dev": {
    "psr-4": {
      "DevScripts\\EnglishNepaliNumberConverter\\Tests\\": "tests/"
    }
  },
  "require": {
    "php": "^8.1"
  },
  "require-dev": {

  },
  "extra": {
    "laravel": {
      "providers": [
        "DevScripts\\EnglishNepaliNumberConverter\\EnglishNepaliNumberConverterProvider"
      ]
    }
  }
}
```

### Create `Convert.php`, `ConvertToEnglish.php`, `ConvertToNepali.php` and  `EnglishNepaliNumberConverterProvider.php` inside `src` folder.

1. Create `Convert.php`
```php
<?php

namespace DevScripts\EnglishNepaliNumberConverter;

class Convert
{
    /**
     * @param string $string
     *
     * @return string
     */
    public static function toNp(string $string): string
    {
        $convertToNepali =  new ConvertToNepali();

        return $convertToNepali->convert($string);
    }

    /**
     * @param string $string
     *
     * @return string
     */
    public static function toEn(string $string): string
    {
        $convertToEnglish =  new ConvertToEnglish();

        return $convertToEnglish->convert($string);
    }
}

```
2. Create `ConvertToEnglish.php`
```php
<?php

namespace DevScripts\EnglishNepaliNumberConverter;

class ConvertToEnglish
{
    /**
     * @var string[]
     */
    private  $nepaliToEnglishArray = [
        '०' => 0,
        '१' => 1,
        '२' => 2,
        '३' => 3,
        '४' => 4,
        '५' => 5,
        '६' => 6,
        '७' => 7,
        '८' => 8,
        '९' => 9,
        "." => ".",
    ];

    /**
     * @inheritDoc
     */
    public function convert(string $str): string
    {
        $number = "";

        foreach(mb_str_split($str) as $value) {
            if(isset($this->nepaliToEnglishArray[$value])) {
                $number .= $this->nepaliToEnglishArray[$value];
            }
        }

        return $number;
    }
}


```
3. Create `ConvertToNepali.php`
```php
<?php

namespace DevScripts\EnglishNepaliNumberConverter;

class ConvertToNepali
{
    /**
     * @var string[]
     */
    private  $englishToNepaliArray = [
        0 => '०',
        1 => '१',
        2 => '२',
        3 => '३',
        4 => '४',
        5 => '५',
        6 => '६',
        7 => '७',
        8 => '८',
        9 => '९',
        '.' => '.',
        ',' => ',',
    ];

    /**
     * @inheritDoc
     */
    public function convert(string $str): string
    {
        $utf = "";

        foreach(str_split($str) as $value) {
            if(isset($this->englishToNepaliArray[$value])) {
                $utf .= $this->englishToNepaliArray[$value];
            }
        }

        return $utf;
    }
}


```
4. Create `EnglishNepaliNumberConverterProvider.php`
```php
<?php

namespace DevScripts\EnglishNepaliNumberConverter;

use Illuminate\Support\ServiceProvider;

class EnglishNepaliNumberConverterProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->make('DevScripts\EnglishNepaliNumberConverter\Convert');
    }

    /**
     * Bootstrap services.
     *
     * @return void
     */
    public function boot()
    {
        //
    }
}

```

### Write some unit test inside `tests` folder

1.  Create `UnitTestCase.php`
```php
<?php

namespace DevScripts\EnglishNepaliNumberConverter\Tests;

use PHPUnit\Framework\TestCase;

class UnitTestCase extends TestCase
{

}
```
2.  Create `Units/ConvertTest.php`

```php
<?php

namespace DevScripts\EnglishNepaliNumberConverter\Tests\Unit;

use DevScripts\EnglishNepaliNumberConverter\Convert;
use DevScripts\EnglishNepaliNumberConverter\Tests\UnitTestCase;

class ConvertTest extends UnitTestCase
{
    public function test_nepali_to_english_one_digit()
    {
        $actual =  Convert::toNp(1);
        $expected = "१";
        $this->assertEquals($expected, $actual);
    }

    public function test_nepali_to_english_small_number()
    {
       $actual =  Convert::toNp(123);
       $expected = "१२३";
       $this->assertEquals($expected, $actual);
    }

    public function test_nepali_to_english_small_number_with_decimal()
    {
        $actual =  Convert::toNp(123.33);
        $expected = "१२३.३३";
        $this->assertEquals($expected, $actual);
    }

    public function test_nepali_to_english_big_number_with_decimal()
    {
        $actual =  Convert::toNp(123456789.05);
        $expected = "१२३४५६७८९.०५";
        $this->assertEquals($expected, $actual);
    }

    public function test_nepali_to_english_big_number_with_decimal_and_thousand_count()
    {
        $number = 123456789.05;
        $numberThousandCount = number_format($number, 2);
        $actual =  Convert::toNp($numberThousandCount);
        $expected = "१२३,४५६,७८९.०५";
        $this->assertEquals($expected, $actual);
    }

    public function test_english_to_nepali_one_digit()
    {
        $actual =  Convert::toEn("१");
        $expected = 1;
        $this->assertEquals($expected, $actual);
    }

    public function test_english_to_nepali_small_number()
    {
        $actual =  Convert::toEn("१२३");
        $expected = 123;
        $this->assertEquals($expected, $actual);
    }

    public function test_english_to_nepali_small_number_with_decimal()
    {
        $actual =  Convert::toEn("१२३.३३");
        $expected = 123.33;
        $this->assertEquals($expected, $actual);
    }

    public function test_english_to_nepali_big_number_with_decimal()
    {
        $actual =  Convert::toEn("१२३४५६७८९.०५");
        $expected = 123456789.05;
        $this->assertEquals($expected, $actual);
    }
}
```

### Create `README.md` file.

Create `README.md` file and update your documentation

Now your package is ready.

## Step #4 Test your package locally

If you want to test locally you need to update the main
`composer.json` file reside in the root of the project. You need to make below changes.

```json
"require": {
"dev-scripts/english-nepali-number-converter-php": "dev-main"
},
"autoload": {
"psr-4": {
"DevScripts\\EnglishNepaliNumberConverter\\": "packages/dev-scripts/english-nepali-number-converter-php/src/"
}
},
"autoload-dev": {
"psr-4": {
"DevScripts\\EnglishNepaliNumberConverter\\Tests\\": "packages/dev-scripts/english-nepali-number-converter-php/tests/"
}
},
```

Run test from root folder.
To run your test you should have `vendor` folder inside the  `english-nepali-number-converter-php`

You already have `composer.json` file inside `english-nepali-number-converter-php` just need to run `composer update` this will install the `phpunit` package  inside `vendor` folder.

Now, run test :
```
test packages/dev-scripts/english-nepali-number-converter-php
```

Also, you can test it via a web URL. You need to mack changes
in` web.php` route file.
```php
Route::get('/', function() {
   echo \DevScripts\EnglishNepaliNumberConverter\Convert::toNp("123454546443564364363464.100001");
    echo \DevScripts\EnglishNepaliNumberConverter\Convert::toEn("१२३४५४५४६४४३५६४३६४३६३४६४.१००००१");
});
```
Run app with below artisan command
```
php artisn serve
```

You can see result in web browser

`http://localhost:8000/`

![Result](/images/posts/create-laravel-composer-package-from-scratch-and-publish-on-packagist/web-output.png#center "Result")

## Step #5: Push to  GitHub Public Repository

Now your package is tested and ready to publish.
To publish your package you need to push it to GitHub
public repository.

I have pushed this package in [GitHub](https://github.com/dev-scripts/english-nepali-number-converter-php)

Package need to be pushed form inside `english-nepali-number-converter-php` folder

After pushing package to the GitHub you can get the package URL

My package URL is : https://github.com/dev-scripts/english-nepali-number-converter-php


## Step #6: Publish to https://packagist.org/

In https://packagist.org/ you need your account.
If you don't have account you can create one. I already have account.

So, I will go to submit page and publish my package for public use.

I aam copying the GitHub public repository URL and pest in
the submit text box.

![Submit package](/images/posts/create-laravel-composer-package-from-scratch-and-publish-on-packagist/submit.png#center "Submit package")

Now, the package is publicly available to use. Anybody
can use your package by running below command in their project

```
composer require dev-scripts/english-nepali-number-converter-php
```


![Public package](/images/posts/create-laravel-composer-package-from-scratch-and-publish-on-packagist/public-package.png#center "Public package")

In this post, we have learned how to create Laravel package,
test it locally, push to GitHub public repository and finally
publish to https://packagist.org/ for public use.

GitHub Repository URL : https://github.com/dev-scripts/english-nepali-number-converter-php

Packagist URL : https://packagist.org/packages/dev-scripts/english-nepali-number-converter-php

