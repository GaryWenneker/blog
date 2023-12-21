---
layout: post
title: 'Sitecore JSS: Using a custom Layout Service configuration'
date: 1970-01-18 20:09:18.000000000 +01:00
---
<p>It's easy to change the output of the JSS Layout Service. You can either choose to use the out of the box Content Resolvers or build your own one as I have shown in my post <strong><a href="https://gary.wenneker.org/sitecore-jss-extending-the-layout-service/"><em>Sitecore JSS: Extending the Layout Service</em></a></strong>. The outcome is that you need to define a Content Resolver and point it to your assembly and having your rendering making use of it. So far so good. But what if you have multiple renderings? Then you need to change them all to make them use your resolver. This creates the need of a global Layout Service Configuration!</p><h3 id="creating-the-configuration-patch">Creating the configuration patch</h3><p>The first thing you learn when you want to use the connected my is testing the configuration. This is done by accessing the endpoint <code>/sitecore/api/layout/render/jss?item=/</code>. </p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/09/2018-09-19-10_01_08-Mozilla-Firefox.png" class="kg-image"><figcaption>And we get the expected result from the Layout Service</figcaption></figure><p></p><p>Now I want to get the endpoint to use my Content Resolver for every rendering on my site. You need to do the following things:</p><ul><li>Create a configuration patch file</li><li>Test the Layout Service configuration</li><li>Configure the app and client end point</li></ul><p></p><h3 id="jssconfigurationpatchfile">JSS Configuration Patch file</h3>
<p>The structure of the Layour Service is easy to explain. You need to set it up under <code>/sitecore/layoutService/configurations/config</code>. The configuration has two elements:</p><ol><li><strong>rendering </strong>- element holds the configuration for the <code>placeholdersResolver</code>, <code>itemSerializer </code>and the <code>renderingContentsResolver</code>. Tampering with the configuration of the two first ones will result in unwanted behaviour or even can break the JSS SDK. The <code>renderingContentsResolver</code> is the one I want to configure because I want to change the way the fields of my components are outputted. Because only data under the fields property has changed the structure stays intact and the SDK will not break.</li><li><strong>serialization </strong>- this element has the configuration for the transformer. In <a href="https://gary.wenneker.org/sitecore-jss-extending-the-layout-service/">my former post</a> I didn't cover this piece of code. What happens here is that you can change the <code>route</code> part of the returned data. For me it was necessary to change the way the fields were returned.</li></ol><p>It is a recommendation to use the <code>ref</code> attribute. It then "copies" the existing configuration and only alters the additions you do in your configuration. This way you will have less conflict when upgrading JSS in the future.</p><pre class=" language-xml line-numbers"><code>
<script>
<configuration xmlns:patch="http://www.sitecore.net/xmlconfig/">
  <sitecore>
   <layoutService>
    <configurations>
    <config name="macaw">
        <rendering ref="/sitecore/layoutService/configurations/config[@name='jss']/rendering">
            <placeholdersResolver type="Sitecore.LayoutService.Placeholders.DynamicPlaceholdersResolver, Sitecore.LayoutService" />
            <itemSerializer type="Sitecore.JavaScriptServices.ViewEngine.LayoutService.JssItemSerializer, Sitecore.JavaScriptServices.ViewEngine" resolve="true">
              <AlwaysIncludeEmptyFields>true</AlwaysIncludeEmptyFields>
            </itemSerializer>
            <renderingContentsResolver type="Macaw.Sitecore.LayoutService.ItemRendering.ContentsResolvers.CustomRenderingContentsResolver, Macaw.JSS.Examples" resolve="true">
              <IncludeServerUrlInMediaUrls>true</IncludeServerUrlInMediaUrls>
            </renderingContentsResolver>
        </rendering>
        <serialization ref="/sitecore/layoutService/configurations/config[@name='jss']/serialization">
            <transformer type="Macaw.Sitecore.JavaScriptServices.ViewEngine.LayoutService.Serialization.CustomLayoutTransformer, Macaw.JSS.Examples" resolve="true" />
        </serialization>
    </config>
    </configurations>
   </layoutService>
  </sitecore>
</configuration>
</script>
</code></pre><p> </p><p></p><h3 id="testthelayoutserviceconfiguration">Test the Layout Service configuration</h3>
<p>But refreshing my endpoint did not give me the right result. Why?</p>
<blockquote>The <strong>jss </strong>portion of this route refers to a particular configuration of the Layout Service, which is intended for use outside of JSS as well.</blockquote><p>Looking at my configuration the name of it is macaw and not jss. This means I don't need to hit the endpoint <code>/sitecore/api/layout/render/<strong><em>jss</em></strong>?item=/</code> but <code>/sitecore/api/layout/render/<strong><em>macaw</em></strong>?item=/</code>. </p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/09/macaw.png" class="kg-image"><figcaption>The right output from the Layout Service</figcaption></figure><p></p><p></p><h3 id="configuretheappandclientendpoint">Configure the app and client end point</h3>
<p>To make sure your app uses the right configuration you must add an extra attribute to your <strong>site configuration </strong>in your client solution and configure the client API in code. Otherwise it will try to use the <code>jss</code> configuration.</p><p></p><p>First add the <code>layoutServiceConfiguration</code> attribute and set it to your configuration name:</p><pre class="language-xml line-numbers"><code>
<script>
<app name="demo" layoutServiceConfiguration="macaw" sitecorePath="/sitecore/content/demo" useLanguageSpecificLayout="true" graphQLEndpoint="/api/demo" inherits="defaults" />
</script>
</code></pre><p>By default the service calls are still routed to the endpoint with the <strong>jss</strong> configuration and it is not done by the action above. </p><p>Diving into the server bundle I found out that there is a property called <code>configurationName</code> which is used to define if another configuration is used rather than the default <code>jss</code> one. You could also use the property <code>serviceUrl</code>, but then you need to construct it yourself.</p><pre class="language-javascript line-numbers"><code>
<script>
if (options === void 0) { options = {}; }
    var _a = options.host, host = _a === void 0 ? '' : _a, _b = options.configurationName, configurationName = _b === void 0 ? 'jss' : _b, serviceUrl = options.serviceUrl;
    if (serviceUrl) {
        return serviceUrl;
    }
    return host + "/sitecore/api/layout/" + verb + "/" + configurationName;
</script>
</code></pre><p>Now open src/RouteHandler.js and search for the <code>getRouteData</code> function. Change the layoutServiceConfig object. next to the <code>host</code> attribute also add the <code>configurationName</code> attribute with your configuration name (<code>macaw</code> in my case).</p><pre class="language-javascript line-numbers"><code>
<script>
function getRouteData(route, language, options = {}) {
  const fetchOptions = {
    layoutServiceConfig: { host: config.sitecoreApiHost, configurationName: 'macaw' },
    querystringParams: { sc_lang: language, sc_apikey: config.sitecoreApiKey },
    requestConfig: options,
  };

  return dataApi.fetchRouteData(route, fetchOptions).catch((error) => {
    console.error('Route data fetch error', error);
    return null;
  });
}
</script>
</code></pre><p>And the result is a connection to your configuration and data on your page! <img src="/content/images/2018/09/party_parrot.gif" alt="party_parrot"></p>
<figure class="kg-image-card kg-width-full"><img src="/content/images/2018/09/result-1.png" class="kg-image"></figure>
