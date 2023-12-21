---
layout: post
title: 'Sitecore JSS: ADAL authentication'
date: 1970-01-18 20:58:35.000000000 +01:00
---
<p>When it comes to providing a login for the visitors of your website a lot needs to be done. With Sitecore JavaScript Services we are now entering a whole new ball game.<br>This post will focus on implementing Microsoft Azure's ADAL. I also shared the <a href="https://github.com/GaryWenneker/jss-adal">JSS template</a> which allows you to start with the ADAL connected JSS site immediately.</p><h3 id="what-is-adal">What is ADAL</h3><blockquote><em>The Windows Azure Authentication Library (ADAL) is a library meant to help developers to take advantage of Active Directory for enabling client apps to access protected resources.</em></blockquote><h3 id="setting-up-your-azure-configuration">Setting up your Azure configuration</h3><ol>
<li>Set up an App Service for your website. Log in to <a href="https://portal.azure.com">https://portal.azure.com</a></li>
<li>Click on <code>Azure Active Directory</code>, click on <code>App registrations</code></li>
</ol>
<p><img src="/content/images/2018/10/appreg.png" alt="appreg"></p>
<ol start="3">
<li>in the top click on the button New Application Registration and then fill in the information you want. Note that the <code>Sign-on URL</code> corresponds to your local JSS site (dev URL).</li>
</ol>
<p><img src="/content/images/2018/10/appcreate01.png" alt="New Application Registration"></p>
<ol start="4">
<li>Hitting the create button we now get an overview of the created registration.</li>
</ol>
<p><img src="/content/images/2018/10/appcreate02.png" alt="appcreate02"></p>
<ol start="5">
<li>When you click on settings you will be able to configure even more stuff. Dive into the Microsoft Azure docs for more info if you want to know more about that.</li>
</ol>
<p><img src="/content/images/2018/10/appcrete03.png" alt="appcrete03"></p>
<h3 id="retrieving-the-information">Retrieving the information</h3><p>Now that you have registered your App you also need to have two keys. When you get more familiar with Azure you know that it's full with ID's and it can be a nightmare to get the right one:</p><ol><li><strong><em>TENANT_ID</em></strong> - This is the ID of the Active Directory Service. You can find it under the properties of the Azure Active Directory tab.</li></ol><figure class="kg-image-card"><img src="/content/images/2018/10/ADprops.png" class="kg-image"></figure><p></p><ol><li><strong><em>CLIENT_ID</em></strong> - This is the ID of your registered App, so go back to App registrations and it shows the ID on the right side.</li></ol><h3 id="creating-your-jss-app">Creating your JSS app</h3><p>When you start a new project of course you want to use all kind of libraries you're familiar with. For this reason I created a template for you which allows you to start right away with ADAL in your project. When you want to know more about Custom Templates take a look <a href="https://jss.sitecore.net/docs/techniques/custom-create-templates">here</a>. </p><p>Now let's create a website with ADAL:</p><blockquote>jss create ichabod react --repository garywenneker/jss-adal</blockquote><p>Please note that ADAL has only been added to the React template (not for Angular and Vue). </p><p>Change to that directory and load up VSCode or whatever poison you're using. <br>After filling in the right stuff and hitting off with `jss start` we should see the actual AD login page:</p><figure class="kg-image-card"><img src="/content/images/2018/10/login.png" class="kg-image"></figure><h3 id="changes-to-make-adal-work">Changes to make ADAL work</h3><p>There are a few files which are different, compared to the original templates.</p><ol><li><strong><em>package.json</em></strong> - added a reference to react-adal</li><li><strong><em>index.js</em></strong> - wrapping the app using ADAL</li><li><strong><em>registerServiceWorker </em></strong>- The service worker is a web API, that helps you cache your assets and other files, so that when the user is offline or on a slow network, he/she can still see results on the screen. As such, it helps you build a better user experience. That's what you should know about service worker's for now. It's all about adding offline capabilities to your site.</li><li><strong><em>added AdalConfig.js</em></strong> - this is the place where you have to add your ID's!!</li></ol><p></p><p>An different way is to check the implementation. It's a fork from the original templates: <a href="https://github.com/GaryWenneker/jss-adal">https://github.com/GaryWenneker/jss-adal</a></p>
