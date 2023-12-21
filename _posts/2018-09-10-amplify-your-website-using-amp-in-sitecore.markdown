---
layout: post
title: Amplify your website using AMP in Sitecore
description: >-
    A great way of combining Google&#x27;s Accelerated Mobile Pages (AMP) in Sitecore using this module to have every page in your Sitecore website an AMP page
date: 2018-03-25 15:46:05.000000000 +01:00
image: >-
  https://images.unsplash.com/photo-1509310202330-aec5af561c6b?ixlib=rb-0.3.5&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=1080&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ&s=3c0cf1a57c9cb78fa6d89a8738f17109
optimized_image: >-
  https://images.unsplash.com/photo-1509310202330-aec5af561c6b?ixlib=rb-0.3.5&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=1080&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ&s=3c0cf1a57c9cb78fa6d89a8738f17109
category: blog
tags:
  - sitecore
  - amp
author: garywenneker
paginate: true
---
So, the customer wants to have some pages in AMP I heard a colleague say. Amp? What's that? Never heard of that. I opened op my digital bible Google and started to search.

## What is AMP?

> AMP or [Accelerated Mobile Pages](https://www.ampproject.org/learn/overview/) is Google's CDN where your indexed mobile pages are served to mobile pages

This means that the AMP version of your page gets indexed. When someone with a mobile device searches on Google and clicks on the link rather than going to your website the cached version of that page is served: the AMP page. It's a CDN of your mobile pages and entirely free. You even get a boost on the search results ranking as a bonus if you use AMP.

## The foundation of AMP

AMP is based on three components

*   **AMP HTML** - is HTML with some restrictions for reliable performance
*   **AMP JS** - ensures the fast rendering of AMP HTML pages
*   **AMP CDN** - can be used to serve cached AMP HTML pages

And so, for every page in your website you should have an AMP page!

![](https://i.giphy.com/f2jS9VckmTzQQ.gif)

## Sitecore to the rescue!

I guess we're not monks from the middle ages. We do not want to manually create an AMP page for every page our website. I think it would be nice to have every page in your site to have an AMP equivalent and have the possibility to override the setting and have a custom designed AMP page.

## Creating AMP pages in Sitecore

My own blog has a setting which allows to output every page also as an AMP page and that was the starting point for me to work out this piece of code wich you can also use in your Sitecore environment.

### From AMP to Sitecore

Without showing some content the AMP page will not show correctly. This is why I'll be using my blog post as the example for this post as well. For this I added a gimmick: a markdown parser. The Sitecore item will hold the raw markdown markup and my controller will transform it into plain HTML.

### AMP Pipeline

For my blog I use [Ghost](https://ghost.org/). It's lightweight and you can deploy and use it on [Azure](https://azure.microsoft.com) and without a domain and SSL it's fully free. It holds a setting for AMP. Turn the setting on and you're done:

![](/assets/img/uploads/2018/03/2018-03-25_0921.png)

What it does is adding some functionality that when you add `/amp` in the page url it serves the AMP variant. The way I want to work with such a process can only be done by a pipeline in Sitecore.

Actually we should have two pipelines, one for the transformation and parsing of the url and one for serving the AMP payout page.

#### PreprocessRequest Pipeline

This pipeline is based on the PreprocessRequest pipeline. This one is executed very early in the initialization of the page. During this stage the sessions object of the HTML context has a null value which forces us to use a different approach. We can do this either by adding a query string parameter or set a cookie. My opinion is the usage of a query string parameter is ugly. We should do this in a later process to make the LayoutResolver Pipeline aware that we are in AMP mode. For this pipeline I created three extension methods:

*   **SetAmpCookie(bool)** - Sets a cookie for future usage. If the paramater is set to true it will expire the cookie.
*   **UseAMP()** - Checks if the URL's path ends with /amp
*   **RewriteUrl()** - For internal use but it will strip the /amp

```js
    public class CheckAmpUrl: PreprocessRequestProcessor
        {
            /// 
            /// Adds a session variable when using AMP and removes the token when not
            /// 
            /// the PreprocessRequestArgs
            public override void Process(PreprocessRequestArgs args)
            {
                Assert.ArgumentNotNull((object)args, nameof(args));

                // remove initial check (expired = true)
                args.HttpContext.SetAmpCookie(true);

                // check if the url is requested with the /amp postfix
                if (args.HttpContext.UseAmp())
                {
                    // set cookie for future usage
                    args.HttpContext.SetAmpCookie();
                    //  rewrite url to get original item
                    args.HttpContext.RewriteUrl();
                }
            }
        }
```

#### LayoutResolver Pipeline

This pipeline checks if the AMP cookie is set and if so it will set the AMP device channel.

### AMP Device channel

The AMP device channel is just a a device. Nothing is added to it, no browser engine, no query string value, no rule. And yes we can use the querystring, but, _I'm more stubborn than a donkey_ :-) The query string version renders my code useless but reason to show this is to demonstrate both ways of getting the job done.

![](/assets/img/uploads/2018/03/2018-03-25_1001.png)

This will only be the area where we set the layout for the AMP page.

```js
    public override void Process(HttpRequestArgs args)
            {

                Assert.ArgumentNotNull(args, "args");

                // Get the default device
                HttpRequestBase request = args.HttpContext.Request;
                if (ExtensionMethods.GetAmpCookie(request))
                {
                    SetAmp();
                }
            }

            private void SetAmp()
            {
                DeviceRecords devices = Sitecore.Context.Item?.Database.Resources.Devices;
                if (devices != null)
                {
                    DeviceItem defaultDevice = devices?.GetAll()?.Where(d => d.Name.ToLower() == "amp").FirstOrDefault();
                    if (defaultDevice != null)
                    {
                        Sitecore.Context.Device = defaultDevice;
                    }
                }
            }
```

![](/assets/img/uploads/2018/03/2018-03-25_0952.png)

### From the page to the AMP page

Google does not like it when you have the same contents on more than one page. First you need to add a canonical url on your page. Then you should have the `amphtml` link pointed to your AMP page.

The markup of an AMP page also is slightly different from a normal web page. The total setup of a page is different so you have to take into account that tags are different too. The `img` tag in AMP is `amp-img` which forces us to rewrite some of the tags. In my source code project I'm providing with this post I use the HTML Agility Pack to do so. Feel free to adapt it to your needs.

The following code will

*   get data from the Sitecore item
*   transform the markdown to HTML
*   if the page is an AMP page it will update the image tags
*   returns the view based on the selected device

```js
    public ActionResult Index()
            {

                var item = Sitecore.Context.Item;
                string md  = item.Fields["Content"].Value;

                // Create new markdown instance
                Markdown mark = new Markdown();

                // Run parser
                string text = mark.Transform(md);

                if (HttpContext.UseAmp())
                    text = UpdateAmpImages(text);

                return View(new Models.BlogPost() { Content = text });
            }
```

### The result

Chrome has a nice [plugin](https://chrome.google.com/webstore/detail/amp-validator/nmoffdblmcmgeicmolmhobpoocbbmknc) you can use to validate your AMP markup. When you do it right the icon will turn green

![](/assets/img/uploads/2018/03/2018-03-25_1046.png)

And eventually you will see your AMP page!

![](/assets/img/uploads/2018/03/post.png)

![](/assets/img/uploads/2018/03/2018-03-25_1008.png)

### Sitecore's AMP solution on Github

The entire solution with serialized items (Unicorn) is available on my [Github page](https://github.com/GaryWenneker/Gary.Sitecore.AMP).

So get your ass overthere and start amplifying your site!

![](https://i.giphy.com/SwT2Pk15RH5m.gif)
