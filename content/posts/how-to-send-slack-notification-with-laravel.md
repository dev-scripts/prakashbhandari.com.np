---
title: "How to Send Slack Notification With Laravel?"
date: 2023-05-17T15:15:37+10:00
draft: false
showToc: true
TocOpen: true
tags : [
"PHP",
"webhooks",
"Slack"
]
categories : [
"PHP",
]
keywords: [
"How to Send Slack Notification With Laravel",
"Slack webhook integration with laravel",
"Slack and laravel",
"Slack webhook",
]
cover:
    image: "images/posts/how-to-send-slack-notification-with-laravel/how-to-send-slack-notification-with-laravel.png" # image path/url
    alt: "How to Send Slack Notification With Laravel ?"
    caption: "How to Send Slack Notification With Laravel ?"
    relative: false
    author: "Prakash Bhandari"
description: "In Laravel, sending notification is very simple. Notification will send the informational messages that will notify users about something has occurred in your application."
---

In Laravel, each notification is represented by a single class that is typically stored in the `app/Notifications` directory. 

Don't worry if you don't see this directory in your application. It will be created for you when you run the below artisan command

`make:notification`

ie. `php artisan make:notification TestNotification.php`

Laravel documentation on how to create notification https://laravel.com/docs/10.x/notifications

Laravel notification can be used for many purpose as for example, sending SMS, Email etc.

In this post, I will discuss on how to send the notification or alert message to slack via Slack webhook url.


# 1. Install slack notification Channel
To integrate Slack notification in Laravel, you need to install the `laravel/slack-notification-channel` package.

You can simply run this command to install laravel package in your project: 

`composer require laravel/slack-notification-channel`

GitHub : https://github.com/laravel/slack-notification-channel

# 2. Create Notification Class
Run below artisan command in your project. 
This command will create `SendSlackNotification.php` class inside `app/Notifications` folder

`php artisan make:notification SendSlackNotification`

New class will be created like this `app/Notifications/SendSlackNotification.php`

# 3. Configure file app/Notifications/SendSlackNotification class
Next configure file `app/Notifications/SendSlackNotification.php` class.

{{< github-code-snippets afcbf15cd7f4e196caa098775a6dcad5 >}}

# 4. Create webhook and assign to Slack channel

## Create Channel
Login to slack and create your notification channel. Here I am creating `#test-slack-notification` channel.


![Create Channel](/images/posts/how-to-send-slack-notification-with-laravel/create-channel.png#center)

## Create Slack app 
Navigate to this URL: https://api.slack.com/apps?new_app=1 to crate Slack app.

![Create slack app App](/images/posts/how-to-send-slack-notification-with-laravel/create-app.png#center)

## Enable Incoming Webhooks
![CEnable Incoming Webhooks webhook](/images/posts/how-to-send-slack-notification-with-laravel/incoming-webhook.png#center)
![Create App](/images/posts/how-to-send-slack-notification-with-laravel/enable-incoming-webhooks.png#center)

## Add Webhook URLs for Your Workspace

![Add Webhook URLs for Your Workspace](/images/posts/how-to-send-slack-notification-with-laravel/add-webhook-urls-for-your-workspace.png#center)

## Copy webhook URL
![Copy webhook URL](/images/posts/how-to-send-slack-notification-with-laravel/copy-webhook-urls.png#center)

# 4. Send Notification

Once you have  *Webhook URL*, you can simply use that URL in you app to send the notification from your Laravel app to Slack channel. 
I have added below code on router to test the Notification.

{{< github-code-snippets b64a808009be0c4514b52707c44bec3d >}}

# 5. Demo

![Demo](/images/posts/how-to-send-slack-notification-with-laravel/how-to-send-slack-notification-with-laravel.gif#center)




