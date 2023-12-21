---
layout: post
title: Turbo boosting Sitecore SXA Rendering Variants
date: 1970-01-18 14:45:46.000000000 +01:00
---
<p>In my post <a href="https://gary.wenneker.org/2018/01/04/sitecore-experience-accelerator/">Tuning Sitecore Experience Accelerator (SXA) with React.Net</a> and <a href="http://gary.wenneker.org/2018/01/21/supercharging-sitecore-with-sxa/">Supercharging Sitecore with SXA</a> I have proven that you can do just do about anything in SXA like the greatness of having the ability to use your own design (markup/html) whenever you like withing Sitecore SXA and use them in the Experience Editor Toolbox and drag and drop your components.</p><p>Of course some of you might think that there's more to SXA than just components and the toolbox and that's true. But it would be unfair to SXA to just cover all of the functionality in one post. So keep coming back and read about my SXA journey because I believe that it is here to stay and Sitecore is going to intensify their work on SXA.</p><p>One of the main purposes of SXA is a faster time to market and this is mainly done by skipping code. How? all configuration is done in the content section and therefore is treated like content. And because it is content we only need to publish it.</p><p>Some other companies may choose to have a team of front-end developer who do not know much about Sitecore and even are added to the team of developers.</p><p><em>Want to skip to the <a>conclusion</a>?</em></p><h2 id="turbo-boosting-rendering-variants">Turbo boosting Rendering Variants</h2><figure class="kg-image-card"><img src="https://ghost-azure-gary.azurewebsites.net/content/images/2018/01/tenor.gif" class="kg-image"></figure><p>Back to turbo boosting rendering variants. But what are rendering variants? Actually it's pretty simple. Imagine a news website. A news item may be shown with a title, an image on the left and a small intro on the right. A rendering variant is just the same data with a different presentation, let's say we want the image on the right and the intro on the left.<br>This can be done with the Rendering Variants in SXA. Even way cooler... from the control box in the Experience Editor we can choose from a drop-down which variant we want to use!!</p><figure class="kg-image-card"><img src="https://ghost-azure-gary.azurewebsites.net/content/images/2018/02/2018-02-04_1239.png" class="kg-image"></figure><p>In my previous post on <a href="http://gary.wenneker.org/2018/01/21/supercharging-sitecore-with-sxa/">Tuning Sitecore SXA</a> I used server side rendering with ReactJS to allow fast development of your own components and scaffolding controls. Issue here is that you only have one visual aspect of the component. Often you need to have multiple presentations of a component, image left, right, above or below, with a teaser text or not. This will soon lead to a lot of components in your toolbox. You can sort them by area but this is not a nice way to manage it.</p><h2 id="adding-an-sxa-rendering-variant">Adding an SXA rendering variant</h2><p>First we need to add only the main component to the toolbox which means we are adding it to the available renderings:</p><figure class="kg-image-card kg-width-full"><img src="https://ghost-azure-gary.azurewebsites.net/content/images/2018/02/2018-02-04_1206.png" class="kg-image"></figure><p>Then we need to add the Rendering variants to this rendering. This is done by adding a Rendering Variant. Because I want to be able to switch back to the original rendering I also add this one as the default:</p><figure class="kg-image-card kg-width-full"><img src="https://ghost-azure-gary.azurewebsites.net/content/images/2018/02/2018-02-04_1209.png" class="kg-image"></figure><h2 id="which-controller-to-use-when-using-sxa">Which controller to use when using SXA</h2><p>As seen below I added the rendering of the variant:</p><figure class="kg-image-card kg-width-full"><img src="https://ghost-azure-gary.azurewebsites.net/content/images/2018/02/2018-02-04_1212.png" class="kg-image"></figure><p>After this I went back to the Experience Editor to see if it works.<br>It didn't. Why? I use a <strong>StandardController</strong> and should have used the <em><strong>VariantsController</strong></em>. So I felt a little like this...(for one week)</p><figure class="kg-image-card"><img src="https://i.giphy.com/N8kmvUsp0gObK.gif" class="kg-image"></figure><p>Use of the VariantsController is a necessity and allows you to get extra information from the variant itself. When working with POCO's you alternatively use an extra repository and inject it into your controller. For non-technical readers: this will be done for you by a developer :-)</p><h2 id="adding-variables-to-sitecore-sxa-rendering-variants">Adding variables to Sitecore SXA rendering variants</h2><p>After fiddling around in the Quickwatch of Visual Studio I came up with the idea of adding a data attribute to the variant and pass it into my controller. This means that I get the Variant Field named <em>Controller</em> and from there I ask for the <em>ModuleName</em> Data Attribute which holds the module name of my React Control.</p><figure class="kg-image-card kg-width-full"><img src="https://ghost-azure-gary.azurewebsites.net/content/images/2018/02/2018-02-04_1217.png" class="kg-image"></figure><p>For this to work we must have some references:</p><ul><li>Sitecore.XA.Foundation.IoC.dll</li><li>Sitecore.XA.Foundation.Variants.Abstractions.dll</li><li>Sitecore.XA.Foundation.RenderingVariants.dll</li><li>Sitecore.XA.Foundation.Mvc.dll</li></ul><p>and eventually some code to pass the variables to the actual property to render the right variant:</p><pre class="line-numbers language-csharp"><code>
using Macaw.XA.Feature.Media.Models;
using Sitecore.Mvc.Presentation;
using Sitecore.Web.UI.WebControls;
using System.Collections.Generic;
using System.Web.Mvc;
using System.Linq;
using Sitecore.XA.Foundation.RenderingVariants.Controllers;
using Sitecore.XA.Foundation.RenderingVariants.Fields;

namespace Macaw.XA.Feature.Media.Controllers
{
    public class HeroController : VariantsController
    {
        public ActionResult React()
        {
            
            var item = RenderingContext.Current.Rendering.Item;
            var helper = RenderingContext.Current.PageContext.HtmlHelper;

            // get variant data from SXA
            var repos = this.VariantsRepository;
            var field = repos.VariantFields.Where(x => x.ItemName.Equals("Controller", System.StringComparison.OrdinalIgnoreCase)).FirstOrDefault();
            var controllerField = field as VariantField;
            string moduleName = controllerField?.DataAttributes["ModuleName"];

            ViewBag.Module = moduleName; 


            var placeholders = new List<ReactPlaceholder>() { };


            return PartialView(new DummyViewModel { Title = FieldRenderer.Render(item, "Title"), Text = FieldRenderer.Render(item, "Body"), Placeholders = placeholders });
        }
    }
}

</code></pre><p>Get the full source <a href="https://github.com/GaryWenneker/Gary.Sitecore.XA">here</a>. Make sure to add your own React server bundle in the scripts directory.</p><p>After publishing all of my configuration (my SXA settings is content so publish it or it doesn't work!) I can choose my component and switch the variant:</p><h2 id="takeaways">Takeaways</h2><p>As <a href="https://twitter.com/adamnaj/status/956052244738334720">said</a> before Sitecore SXA is a development framework. All roads lead to Rome when it comes to finding the right solution to implement what's the right way for your implementation. Adding the VariantsController adds even more functionality to your experience and will help the content editor to keep overview and ease of using all the greatness in Sitecore. Now we can easily choose a React variant from a drop-down box instead of choosing one from the control box and selecting a compatible rendering.</p><figure class="kg-image-card"><img src="https://i.giphy.com/UuDxS2EBRZSyA.gif" class="kg-image"></figure>
