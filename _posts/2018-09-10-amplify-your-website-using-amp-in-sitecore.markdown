---
layout: post
title: Amplify your website using AMP in Sitecore
description: >-
    A great way of combining Google&#x27;s Accelerated Mobile Pages (AMP) in Sitecore using this module to have every page in your Sitecore website an AMP page
date: 2020-02-14 15:46:05.000000000 +01:00
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
<p>So, the customer wants to have some pages in AMP I heard a colleague say. Amp? What's that? Never heard of that. I opened op my digital bible Google and started to search.</p>
<h2 id="whatisamp">What is AMP?</h2>
<blockquote>
<p>AMP or <a href="https://www.ampproject.org/learn/overview/">Accelerated Mobile Pages</a> is Google's CDN where your indexed mobile pages are served to mobile pages</p>
</blockquote>
<p>This means that the AMP version of your page gets indexed. When someone with a mobile device searches on Google and clicks on the link rather than going to your website the cached version of that page is served: the AMP page. It's a CDN of your mobile pages and entirely free. You even get a boost on the search results ranking as a bonus if you use AMP.</p>
<h2 id="thefoundationofamp">The foundation of AMP</h2>
<p>AMP is based on three components</p>
<ul>
<li><strong>AMP HTML</strong> - is HTML with some restrictions for reliable performance</li>
<li><strong>AMP JS</strong> - ensures the fast rendering of AMP HTML pages</li>
<li><strong>AMP CDN</strong> - can be used to serve cached AMP HTML pages</li>
</ul>
<p>And so, for every page in your website you should have an AMP page!</p>
<p><img src="https://i.giphy.com/f2jS9VckmTzQQ.gif" alt=""></p>
<h2 id="sitecoretotherescue">Sitecore to the rescue!</h2>
<p>I guess we're not monks from the middle ages. We do not want to manually create an AMP page for every page our website. I think it would be nice to have every page in your site to have an AMP equivalent and have the possibility to override the setting and have a custom designed AMP page.</p>
<h2 id="creatingamppagesinsitecore">Creating AMP pages in Sitecore</h2>
<p>My own blog has a setting which allows to output every page also as an AMP page and that was the starting point for me to work out this piece of code wich you can also use in your Sitecore environment.</p>
<h3 id="fromamptositecore">From AMP to Sitecore</h3>
<p>Without showing some content the AMP page will not show correctly. This is why I'll be using my blog post as the example for this post as well. For this I added a gimmick: a markdown parser. The Sitecore item will hold the raw markdown markup and my controller will transform it into plain HTML.</p>
<h3 id="amppipeline">AMP Pipeline</h3>
<p>For my blog I use <a href="https://ghost.org/">Ghost</a>. It's lightweight and you can deploy and use it on <a href="https://azure.microsoft.com">Azure</a> and without a domain and SSL it's fully free. It holds a setting for AMP. Turn the setting on and you're done:</p>
<p><img src="https://gary.wenneker.org/content/images/2018/03/2018-03-25_0921.png" alt=""></p>
<p>What it does is adding some functionality that when you add <code>/amp</code> in the page url it serves the AMP variant. The way I want to work with such a process can only be done by a pipeline in Sitecore.</p>
<p>Actually we should have two pipelines, one for the transformation and parsing of the url and one for serving the AMP payout page.</p>
<h4 id="preprocessrequestpipeline">PreprocessRequest Pipeline</h4>
<p>This pipeline is based on the PreprocessRequest pipeline. This one is executed very early in the initialization of the page. During this stage the sessions object of the HTML context has a null value which forces us to use a different approach. We can do this either by adding a query string parameter or set a cookie. My opinion is the usage of a query string parameter is ugly. We should do this in a later process to make the LayoutResolver Pipeline aware that we are in AMP mode. For this pipeline I created three extension methods:</p>
<ul>
<li><strong>SetAmpCookie(bool)</strong> - Sets a cookie for future usage. If the paramater is set to true it will expire the cookie.</li>
<li><strong>UseAMP()</strong> - Checks if the URL's path ends with /amp</li>
<li><strong>RewriteUrl()</strong> - For internal use but it will strip the /amp</li>
</ul>
<pre class="line-numbers language-csharp"><code>
public class CheckAmpUrl: PreprocessRequestProcessor
    {
        /// <summary>
        /// Adds a session variable when using AMP and removes the token when not
        /// </summary>
        /// <param name="args">the PreprocessRequestArgs</param>
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
</code></pre>
<h4 id="layoutresolverpipeline">LayoutResolver Pipeline</h4>
<p>This pipeline checks if the AMP cookie is set and if so it will set the AMP device channel.</p>
<h3 id="ampdevicechannel">AMP Device channel</h3>
<p>The AMP device channel is just a a device. Nothing is added to it, no browser engine, no query string value, no rule. And yes we can use the querystring, but, <em>I'm more stubborn than a donkey</em> :-) The query string version renders my code useless but reason to show this is to demonstrate both ways of getting the job done.</p>
<p><img src="/content/images/2018/03/2018-03-25_1001.png" alt=""></p>
<p>This will only be the area where we set the layout for the AMP page.</p>
<pre class="line-numbers language-csharp"><code>
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
</code></pre>
<p><img src="/content/images/2018/03/2018-03-25_0952.png" alt=""></p>
<h3 id="fromthepagetotheamppage">From the page to the AMP page</h3>
<p>Google does not like it when you have the same contents on more than one page. First you need to add a canonical url on your page. Then you should have the <code>amphtml</code> link pointed to your AMP page.</p>
<p>The markup of an AMP page also is slightly different from a normal web page. The total setup of a page is different so you have to take into account that tags are different too. The <code>img</code> tag in AMP is <code>amp-img</code> which forces us to rewrite some of the tags. In my source code project I'm providing with this post I use the HTML Agility Pack to do so. Feel free to adapt it to your needs.</p>
<p>The following code will</p>
<ul>
<li>get data from the Sitecore item</li>
<li>transform the markdown to HTML</li>
<li>if the page is an AMP page it will update the image tags</li>
<li>returns the view based on the selected device</li>
</ul>
<pre class="line-numbers language-csharp"><code>
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
</code></pre>
<h3 id="theresult">The result</h3>
<p>Chrome has a nice <a href="https://chrome.google.com/webstore/detail/amp-validator/nmoffdblmcmgeicmolmhobpoocbbmknc">plugin</a> you can use to validate your AMP markup. When you do it right the icon will turn green</p>
<p><img src="/content/images/2018/03/2018-03-25_1046.png" alt=""></p>
<p>And eventually you will see your AMP page!</p>
<p><img src="/content/images/2018/03/post.png" alt=""></p>
<p><img src="/content/images/2018/03/2018-03-25_1008.png" alt=""></p>
<h3 id="sitecoresampsolutionongithub">Sitecore's AMP solution on Github</h3>
<p>The entire solution with serialized items (Unicorn) is available on my <a href="https://github.com/GaryWenneker/Gary.Sitecore.AMP">Github page</a>.</p>
<p>So get your ass overthere and start amplifying your site!</p>
<p><img src="https://i.giphy.com/SwT2Pk15RH5m.gif" alt=""></p>
