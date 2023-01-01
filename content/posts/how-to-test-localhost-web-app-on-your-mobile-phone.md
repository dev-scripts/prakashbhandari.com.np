---
title: "How to Test Localhost Web App on Your Mobile Phone?"
date: 2023-01-01T11:09:28+11:00
draft: false
showToc: true
TocOpen: true
tags : [
"Development Environment",
]
categories : [
"Development Environment",
]
keywords: [
"How to Test Localhost Web App on Your Mobile Phone?",
"How to see your Localhost Website on your Mobile phone",
"Access Localhost form mobile",
"How to test a local website on your phone",
"How to access localhost website on mobile browser",
"How to access localhost environment on mobile browser",
]
cover:
    image: "images/posts/how-to-test-localhost-web-app-on-your-mobile-phone/how-to-test-localhost-web-app-on-your-mobile-phone.png"
    alt: "How to Test Localhost Web App on Your Mobile Phone?"
    caption: "How to Test Localhost Web App on Your Mobile Phone?"
    relative: false
author: "Prakash Bhandari"
---

The computer was the main tool used to get information from the Internet. However, recent
evolution in smart devices, such are smartphones or tablets enabled mobile users to have the same power that computer had.
User can browse websites in their own smart devices via mobile web browsers with the same speed of internet from anywhere.

As one of the most dispersed communication tools in the world today, the mobile phone
technology has a growing impact on the social and cultural aspect of everyday life of
individuals.

In the early 90s, web design was very simple. Most websites used simple HTML tags and build static pages. 
Used to developed in computer and browsed via computer browsers.

Evolution of different size and type of devices, responsive web design was also evolved. The website is developed in computer/laptop 
but widely browsed via mobile devices. As a result, testing localhost (locally building) web app is very important to validate the device compatability
You can not always perfectly validate the device compatability via computer browser's tool. Sometimes you need to test your localhost Web App via real mobile device.

Testing localhost web app on your mobile phone is not that hard. I will show you few steps on how to access
localhost web app via mobile devices.

# Step 1: Connect your both devices to the same network.

 Make sure that your computer which is used build your web app locally and your mobile device which is used to test the web app should be connected to the same WiFi network.

# Step 2: Find your local IP address of your computer
We need to get the IPV4 form computer. There are different ways of finding the IP address of the different OS.
## MacOS
In MacOS you can get IP addredd in two ways:

1. If you are using WiFi then run `ipconfig getifaddr en0` command in terminal.
2. If you are using Ethernet then run `ipconfig getifaddr en1` command in the terminal.

Both commands will give you the exact IP address of your computer.

You can also use the  UI to find the IP address. But, I always use the command line.

## Linux
In `Linux` you can simply run the  `ifconfig` in the terminal. This command will give you the exact IP address of your computer.
## Windows

In `Windows` you can simply run the  `ipconfig` in the terminal. This command will give you the exact IP address of your computer.
You can also use the windows UI to find the IP address. But, I always use the command line.

# Step 3: Run Your Web App Locally

Run your web app locally. You will get the local URL for an app to browse via computer browser.

The local web app URl could look like:

`http://localhost:3000/`

# Step 4: Generate URL to Access your Web App Within the Network

In **Step 2** you will get the IP address of the local machine and in **Step 3** you will get the full local URL for the web app.
In this step you need to combine those two steps.

Suppose your local IP address of computer is **192.168.1.173** and local web app URl is  **http://localhost:3000/**

Now, new URL to Access your Web App Within the Network will be like :

**http://192.168.1.173:3000**

# Step 5: Open URL in Your Computer and Mobile Browser

Now, you have generated the network URL in **step 4**. You can use same URL (IP_ADDRESS:PORT_NUMBER) in your mobile browser.
This should browse web app form your computer to mobile device.
Your computer will act as a server and mobile device will act as a client.
### View in Computer Browser
You can use `http://localhost:3000/` to browse in your computer. You can see below result in the computer.

![How to Test Localhost Web App on Your Mobile Phone?](/images/posts/how-to-test-localhost-web-app-on-your-mobile-phone/computer-output.png#center)
### View in Mobile Browser
You can use `http://192.168.1.173:3000` to browse in your mobile. You can see below result in the mobile device.
![How to Test Localhost Web App on Your Mobile Phone?](/images/posts/how-to-test-localhost-web-app-on-your-mobile-phone/phone-output.jpg#center)


Hope, you have learned how to browse **Localhost Web App** on Your Mobile Phone.


# YouTube Demo Video
Here is the demo video  on **How to Test Localhost Web App on Your Mobile Phone?**

{{< youtube oltB6kCZ6d0 >}}
