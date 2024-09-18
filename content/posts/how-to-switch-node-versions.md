---
title: "How to Switch Node Versions?"
date: 2023-05-18T14:48:48+10:00
draft: true
showToc: true
TocOpen: true
tags : [
"NodeJS",
"NVM",
"NPM",
"Tools"
]
categories : [
"Node",
]
keywords: [
"Switch node versions windows",
"Switch node versions Mac",
"Switch node versions Linux",
"Node Version Manager (NVM)",
]
cover:
    image: "images/posts/how-to-switch-node-versions/how-to-switch-node-versions.png"
    alt: "How to Switch Node Versions?"
    caption: "How to Switch Node Versions?"
    relative: false
    author: "Prakash Bhandari"
description: "In this article, I will be explaining how to switch the version of Node installed in your machine. I will explain how Node Version Manager (NVM) is used to manage or switch between node versions."
---

You might be working on many node projects on your device. You may be using different versions of Node for different projects.
There could be an issue while running older projects in new version of node. 

As for example, let's say you have created one project 2 years ago which is compatible with node version `v14.17.6` .
Now, you have updated the node version of your machine  to `v20.2.0`.  In this case, your older project might not work
in the newer version of node.

In this case, you need to downgrade your node version to `v14.17.6` or you need to update all the package and make it compatible with
node version `v20.2.0`.

Which is good option ? I don't think both are not good option. Because downgrading is not good idea and updating packages to new might not possible or time consuming.

Then, what should we do?

It would be great if we have an option to switch between node versions. 

Is it possible to switch between node versions in your device? 

Yes, it's possible. You can use Node Version Manager (NVM) to manage or switch between node versions.

If you use docker locally, you never encounter this situation. Only, non docker user will encounter this issue.

# Node Version Manager (NVM) 

NVM is a tool to manage the version for node.js installed in your machine, designed to be installed per-user, and invoked per-shell. nvm works on any POSIX-compliant shell (sh, dash, ksh, zsh, bash), in particular on these platforms: unix, macOS, 
and windows WSL.

GitHub Page : https://github.com/nvm-sh/nvm

## How to Install NVM on Windows 
You can follow the guide form readme to download and install NVM on Windows machine 

Readme : https://github.com/coreybutler/nvm-windows#readme

## How to Install NVM on Linux and Mac
In your terminal, run the nvm installer like this:

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

# or

wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

These commands will clone the `nvm` repository to a `~/.nvm` directory on your device.

Once, installation is completed you can check the version by running below command in your device. This should show the version of nvm installed.

`nvm -v`

# Install and Switch Node versions

Once  `nvm` is installed in your device, you can now install, uninstall, and switch between
different Node versions 

To demonstrate, I am going to install node version `v20.2.0` and `v14.17.6` via NVM.

**Install node version `v20.2.0`**

`nvm install v20.2.0` 

**Install node version `v14.17.6`**

`nvm install v14.17.6`

Now, we can use `nvm use v20.2.0 ` and `nvm use v14.17.6` to switch between two versions.




