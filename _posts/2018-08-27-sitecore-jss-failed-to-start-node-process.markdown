---
layout: post
title: 'Sitecore JSS: Failed to start Node process'
description: >-
  Fix in WIndows using NVM when you get Failed to start Node process. ...
date: 2018-08-27 08:21:12.000000000 +01:00
image: >-
  https://images.unsplash.com/photo-1516031190212-da133013de50?ixlib&#x3D;rb-0.3.5&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ&amp;s&#x3D;80e62784337e34f006324a50bfb9018f
optimized_image: >-
  https://images.unsplash.com/photo-1516031190212-da133013de50?ixlib&#x3D;rb-0.3.5&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ&amp;s&#x3D;80e62784337e34f006324a50bfb9018f
category: blog
tags:
  - jss
  - nodejs
  - nvm
  - sitecore
  - reactjs
author: garywenneker
paginate: true
---

<p>JSS is Sitecore's answer to have the ability to offload your website from sitecore and run the website somewhere else. There are several ways to run the site, disconnected, connected and integrated.</p><blockquote>JSS is a complete SDK for JavaScript developers that enables you to build full-fledged solutions using Sitecore and modern JavaScript UI libraries and frameworks. You can develop completely disconnected from Sitecore and even get your project started without a Sitecore install.</blockquote><h2 id="running-jss-integrated-in-sitecore">Running JSS integrated in Sitecore</h2><p>During my exploration of JSS I also wanted to deploy my app to Sitecore and check if I could open the Experience Editor and edit the fields. For this I used the command <code>jss deploy app --includeContent --includeDictionary</code>. After checking the Content Editor I actually had the node I expected:</p><figure class="kg-image-card"><img src="/assets/img/uploads/2018/08/0002018-08-27-08_36_35-Desktop.png" class="kg-image"></figure><p>From there I opened the Expecience Editor but got the following message:</p><blockquote>Error Rendering Sitecore.JavaScriptServices.ViewEngine.Presentation.JsLayoutRenderer: Failed to start Node process. To resolve this:. [1] Ensure that Node.js is installed and can be found in one of the PATH directories. Current PATH enviroment variable is: C:\Users\garyw\AppData\Roaming\nvm;<strong>C:\Program Files\nodejs</strong>; Make sure the Node executable is in one of those directories, or update your PATH. [2] See the InnerException for further details of the cause.<br>at<br>Sitecore.JavaScriptServices.ViewEngine.NodeServices.HostingModels<br>.OutOfProcessNodeInstance.LaunchNodeProcess(ProcessStartInfo startInfo)</blockquote><h2 id="checking-versions-nvm-and-jss">Checking versions: NVM and JSS</h2><p>So what's going on? I have:</p><ul><li>latest JSS CLI</li><li>latest version of <a href="https://github.com/coreybutler/nvm-windows">nvm</a></li><li>latest version of Node</li><li>NodeJS in the path variables</li></ul><h2 id="fixing-nvm-nodejs-issue-in-iis">Fixing NVM NodeJS issue in IIS</h2><p>Our site and also Sitecore is running in IIS (Internet Information Server) which means under a different user context. By default NVM is not installed globally but in <code>C:\Users\username\AppData\Roaming\nvm</code>. The application pool account is not allowed to execute the NodeJS executable.</p><figure class="kg-image-card"><img src="/assets/img/uploads/2018/08/2018-08-27-08_11_01-nvm-1.png" class="kg-image"></figure><p>Adding everyone to the NVM folder will result in a working Experience Editor</p><figure class="kg-image-card"><img src="/assets/img/uploads/2018/08/2018-08-27-08_11_58-Welcome-to-Sitecore-JSS-1.png" class="kg-image"></figure>
