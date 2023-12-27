---
layout: post
title: Sitecore JSS Adal Authentication
description: >-
  This post shows how to implement ADAL authentication into your Sitecore JavaScripty Services website
date: 2019-05-23 08:21:12.000000000 +01:00
image: >-
  https://images.unsplash.com/photo-1461685265823-f8d5d0b08b9b?ixlib&#x3D;rb-0.3.5&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ&amp;s&#x3D;fc182ab21670216852aac0cb47a4d0b8
optimized_image: >-
  https://images.unsplash.com/photo-1461685265823-f8d5d0b08b9b?ixlib&#x3D;rb-0.3.5&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ&amp;s&#x3D;fc182ab21670216852aac0cb47a4d0b8
category: blog
tags:
  - jss
  - sitecore
  - graphql
  - nodejs
  - reactjs
  - headless
  - cms
author: garywenneker
paginate: true
---
When it comes to providing a login for the visitors of your website a lot needs to be done. With Sitecore JavaScript Services we are now entering a whole new ball game.  
This post will focus on implementing Microsoft Azure's ADAL. I also shared the [JSS template](https://github.com/GaryWenneker/jss-adal) which allows you to start with the ADAL connected JSS site immediately.

### What is ADAL

> _The Windows Azure Authentication Library (ADAL) is a library meant to help developers to take advantage of Active Directory for enabling client apps to access protected resources._

### Setting up your Azure configuration

1.  Set up an App Service for your website. Log in to [https://portal.azure.com](https://portal.azure.com)
2.  Click on `Azure Active Directory`, click on `App registrations`

![appreg](/assets/img/uploads/2018/10/appreg.png)

1.  in the top click on the button New Application Registration and then fill in the information you want. Note that the `Sign-on URL` corresponds to your local JSS site (dev URL).

![New Application Registration](/assets/img/uploads/2018/10/appcreate01.png)

1.  Hitting the create button we now get an overview of the created registration.

![appcreate02](/assets/img/uploads/2018/10/appcreate02.png)

1.  When you click on settings you will be able to configure even more stuff. Dive into the Microsoft Azure docs for more info if you want to know more about that.

![appcrete03](/assets/img/uploads/2018/10/appcrete03.png)

### Retrieving the information

Now that you have registered your App you also need to have two keys. When you get more familiar with Azure you know that it's full with ID's and it can be a nightmare to get the right one:

1.  **_TENANT_ID_** - This is the ID of the Active Directory Service. You can find it under the properties of the Azure Active Directory tab.

<figure class="kg-image-card">![](/assets/img/uploads/2018/10/ADprops.png)</figure>

1.  **_CLIENT_ID_** - This is the ID of your registered App, so go back to App registrations and it shows the ID on the right side.

### Creating your JSS app

When you start a new project of course you want to use all kind of libraries you're familiar with. For this reason I created a template for you which allows you to start right away with ADAL in your project. When you want to know more about Custom Templates take a look [here](https://jss.sitecore.net/docs/techniques/custom-create-templates).

Now let's create a website with ADAL:

> jss create ichabod react --repository garywenneker/jss-adal

Please note that ADAL has only been added to the React template (not for Angular and Vue).

Change to that directory and load up VSCode or whatever poison you're using.  
After filling in the right stuff and hitting off with `jss start` we should see the actual AD login page:

<figure class="kg-image-card">![](/assets/img/uploads/2018/10/login.png)</figure>

### Changes to make ADAL work

There are a few files which are different, compared to the original templates.

1.  **_package.json_** - added a reference to react-adal
2.  **_index.js_** - wrapping the app using ADAL
3.  **_registerServiceWorker_ **- The service worker is a web API, that helps you cache your assets and other files, so that when the user is offline or on a slow network, he/she can still see results on the screen. As such, it helps you build a better user experience. That's what you should know about service worker's for now. It's all about adding offline capabilities to your site.
4.  **_added AdalConfig.js_** - this is the place where you have to add your ID's!!

An different way is to check the implementation. It's a fork from the original templates: [https://github.com/GaryWenneker/jss-adal](https://github.com/GaryWenneker/jss-adal)