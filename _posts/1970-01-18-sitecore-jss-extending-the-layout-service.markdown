---
layout: post
title: 'Sitecore JSS: Extending the Layout Service'
date: 1970-01-18 20:01:01.000000000 +01:00
---
<p>JSS (JavaScript Services) is Sitecore's answer to the need of having a decoupled CMS. One of the reasons for the popularity of the headless architecture is because you can redesign your CMS without setting up a new implementation of the CMS itself. A second reason is that developers do not need to know anything about the backend system. JSS is highly flexible and out of the box it supports a a lot when it comes to customization. Nevertheless, we sometimes need to extend the reality as we see it  ;-)</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/09/demo.png" class="kg-image"><figcaption>example output of Sitecore JSS Layout Service</figcaption></figure><h3 id="customization-areas">Customization areas</h3><p>JSS is highly flexible and has a lot of options to get your data from the service endpoint. And if you want to do even more you can get it through GraphQL out of the box by configuring a query on the rendering. And if that isn't enough you can extend the layout service. Extending can be done in the following areas:</p><ul>
<li><strong>Layout Service context</strong> - You can add/change data about the page or site.</li>
<li><strong>Rendering contents</strong> - Change the way the component data is presented. These are the field values.</li>
<li><strong>Field Serializers</strong> - Change the way the field is outputted.</li>
</ul>
<p>In this post I will show you how easy it is to change the rendering contents.</p><h3 id="customizing-the-layout-service-rendering-output">Customizing the Layout Service Rendering Output</h3><p>There are three ways of changing the output of the service to your needs. Two ways can be done by configuration and is done with ease through the content editor:</p><p><strong>Configuring the built in Rendering <em><a href="https://gary.wenneker.org/sitecore-jss-create-you-own-graphql-schema/">GraphQL</a> </em>support</strong> - If you are new to GraphQL make sure to check out my post <a href="https://gary.wenneker.org/sitecore-jss-create-you-own-graphql-schema/">Sitecore JSS: Create your own GraphQL schema</a>.<strong> </strong>GraphQL is the new way of querying an API with a JSON-payload. Some like REST, but much more flexible and maintainable. In regard to configuring the queries two tastes can be chosen here: <code>connected mode</code> or <code>integrated mode</code>. With the <code>connected mode</code> data is passed through the GraphQL query from the client code. The layout service will not handle this request and you surely have to take care that you've selected. Using the <code>integrated mode</code> the data is returned through the layout service.</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/09/graphql-integrated.png" class="kg-image"><figcaption><em>Example of integrated mode</em></figcaption></figure><p><strong>Configuring a builtin Rendering Contents Resolver</strong> - The content resolvers are a builtin mechanism which allows you to configure a rendering in that way that the data returned matches the configuration. When you install JSS, you will get five resolvers out of the box:</p>
<ul>
<li><code>Datasource Resolver</code> - This is the default and serializes your datasource.</li>
<li><code>Datasource Item Children Resolver</code> - Serializes the children of the datasource but not itself.</li>
<li><code>Context Item Resolver</code> - Serializes the context item.</li>
<li><code>Context Item Children Resolver</code> - Serializes the children of the context item.</li>
<li><code>Folder Filter Resolver</code> - Serializes the descendents of a datasource, but not folders (because of the <em>Item Selector Query</em>: <code>.//*[@@templateid!='{A87A00B1-E6DB-45AB-8B54-636FEC3B5523}']</code>)</li>
</ul>
<p>​</p>
<blockquote>
<p>The Item Selector Query is a very powerful mechanism but use it with care because it can influence the performance</p>
</blockquote>
<p>​</p>
<p><img src="/content/images/2018/09/contentresolvers.png" alt="contentresolvers"></p>
<p><strong>Create a RenderingContentsResolver</strong> - And because I love programming this is my favorite <img src="/content/images/2018/09/party_parrot.gif" alt="party_parrot">!! If any of the out of the box solutions does not fit your needs, you must code it yourself. And this time I want to change the way the data is serialized and returned from the service. Fist, we are going to create a C# resolver class. There are two options to do so. You can choose to implement the <code>IRenderingContentsResolver</code> interface and build the implement the logic yourself or inherit from the <code>RenderingContentsResolver</code> class. The second one is great as it has some methods to override which are usefull when you want to resolve items from elsewhere in the content tree.</p>
<p>Then we have to create the Content Resolver item and bind it to the class and the assembly name. We leave <code>use context item</code> unchecked because we want to use our datasource and even extend the template.</p>
<p><img src="/content/images/2018/09/customitem.png" alt="customitem"></p>
<p>Now we need to create the custom code. No extra configuration in Sitecore is needed to be able to use this code. So its sufficient when the assembly resides in the bin-folder.</p>
<p>And then the code:</p>
<pre><code class="line-numbers language-javascript">
using Newtonsoft.Json.Linq;
using Sitecore.Data.Items;
using Sitecore.LayoutService.Configuration;
using Sitecore.LayoutService.ItemRendering.ContentsResolvers;
using Sitecore.Mvc.Presentation;
using System.Collections.Generic;
using Foundation.Macaw.Dictionary.Helpers;
using Foundation.Macaw.Items.Interfaces;
using Foundation.Macaw.Modeling.Helpers;
using Foundation.Macaw.Modeling.Interfaces;
using Foundation.Macaw.Modeling.Models.ViewModels;
using System.Web.Mvc;

namespace Macaw.Sitecore.LayoutService.ItemRendering.ContentsResolvers
{
    public class CustomRenderingContentsResolver : RenderingContentsResolver
    {
        protected readonly IItemService _itemService;
        protected readonly IFeatureModelService _featureModelService;

        public CustomRenderingContentsResolver()
        {
            _itemService = DependencyResolver.Current.GetService<IItemService>(); 
            _featureModelService = DependencyResolver.Current.GetService<IFeatureModelService>(); 
        }

        public override object ResolveContents(Rendering rendering, IRenderingConfiguration renderingConfig)
        {
            var datasource = !string.IsNullOrEmpty(rendering.DataSource)
                ? rendering.RenderingItem?.Database.GetItem(rendering.DataSource)
                : null;

            var boilerplate = new {
                    datasource.ID,
                    datasource.Name,
                    Datasource = GetItemViewModel(),
                    Parameters = RenderingParametersModelHelper.GetRenderingParameters(), 
                    Dictionary = DictionaryItemsHelper.GetRenderingDictionaryItems(), 
                };

            object json =  base.ResolveContents(rendering, renderingConfig);

            return new { fromContentResolver = json, macaw = boilerplate };
        }

        private ItemViewModel GetItemViewModel()
        {
            var item = _itemService.GetLayoutItem();
            return ViewModelHelper.GetItemViewModel(item);
        }

    }
}
</code>
</pre><p>Just drop the dll in the bin folder and make sure the binding in the resolver item in the content editor matches. Then let's move to the rendering and select our created item:</p>
<p><img src="/content/images/2018/09/select.png" alt="select"></p>
<p>Secondly, we want to change our template to give it some more data:</p>
<p><img src="/content/images/2018/09/template.png" alt="template"></p>
<p>Now let's fill in the fields of the item we had:</p>
<p><img src="/content/images/2018/09/item.png" alt="item"></p>
<p>And finally, let's request our data from the API:</p>
<p><img src="/content/images/2018/09/zzz.png" alt="zzz"></p>
<p>From the code you can see that I output an <a href="https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/anonymous-types">anonymous type</a> holding two properties. The first one is the resolved data by JSS (<em>fromContentResolver</em>) and the second part is the contents that are resolved by the custom logic (<em>macaw</em>). There are two things that stand out right away when you look at the field structure returned from the layout service. The data in the fromContentResolver part hold all field values in a list having its name and a value property which exposes the value. In addition, it also got the data from the rest of the resolved items from my custom logic. The custom logic, however, holds business logic like inheritance of field values. This means that when the SEO title is blank it will take the title of the parent. </p><p><img src="https://i.giphy.com/y8mThMjGBrQpG.gif" alt=""></p>
