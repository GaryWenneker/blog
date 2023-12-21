---
layout: post
title: 'Sitecore JSS: Authentication'
date: 1970-01-18 20:19:33.000000000 +01:00
---
<p>Building a website at some point always requires some mechanism for a user to be able to login. And because we are working in a headless enviroment we have to check if we can accomplish this.</p><p> </p><h3 id="jss-authentication-recipe">JSS Authentication Recipe</h3><p>Before we can actually log in we must have some some stuff in our JSS project. Most of the time we execute GET-requests and consume the data we receive from the server. Now we have to add user interaction and also post data to the server.</p><p>I want to have some state in my app and that means that I have to add <code>react-redux</code> and for my controls I want to use material design so I also add <code>material-ui</code> to my package.json. This allows me to create a page with a login interface.</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/09/login.png" class="kg-image"><figcaption>My great login page</figcaption></figure><p>Next we have to make sure our login component will work with our app. The app also has to work with our Redux store. This means that we need to wrap the ApolloProvider in the Redux Provider. Dont forget to import Provider and Store: <code>import { Provider } from 'react-redux';</code> <code>import { store } from './helpers';</code></p><pre class="language-jsx line-numbers"><code><script>
const AppRoot = ({ path, Router, graphQLClient }) => {
  const routeRenderFunction = (props) => <RouteHandler route={props} />;
  return (
    <Provider store={store}>
      <ApolloProvider client={graphQLClient}>
        <SitecoreContext componentFactory={componentFactory} contextFactory={SitecoreContextFactory}>
          <Router location={path} context={{}}>
            <Switch>
              {routePatterns.map((routePattern) => (
                <Route key={routePattern} path={routePattern} render={routeRenderFunction} />
              ))}
            </Switch>
          </Router>
        </SitecoreContext>
      </ApolloProvider>
    </Provider>
  );
};

export default AppRoot;
</script></code></pre><p>So adding some actions, services, a reducer and a store should work, right??  Wrong!!!!</p><h3 id="bending-the-rules">Bending the rules</h3><blockquote>As the SSC Auth service does not support an <code>OPTIONS</code> preflight CORS request out of the box (resulting in a <code>404</code>response), you will also have to ensure your Sitecore instance responds to the preflight request and returns a <code>200</code> response (with the configured headers).</blockquote><h3 id="the-cors-hell">The CORS hell</h3><p>And I don't mean those hot Irish girls!</p><figure class="kg-image-card"><img src="/content/images/2018/09/The-Corrs-the-corrs-26422784-1024-768.jpg" class="kg-image"><figcaption>Hot Corrs Chicks :-)</figcaption></figure><p>So what's going on here? It turns out that <a href="https://doc.sitecore.net/sitecore_experience_platform/developing/developing_with_sitecore/sitecoreservicesclient/sitecoreservicesclient">SSC</a> out of the box does not allow <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS">CORS</a> preflight requests. So what is this preflight thingy? Only simple requests are allowed directly to a server: <code>GET</code>, <code>HEAD</code> and <code>POST</code>. And posting is only allowed with the current content type headers: <code>application/x-www-form-urlencoded</code>, <code>multipart/form-data</code> or <code>text/plain</code>. Now let's analyse the call to the <a href="https://doc.sitecore.net/sitecore_experience_platform/developing/developing_with_sitecore/sitecoreservicesclient/the_restful_api_for_the_itemservice">SSC API</a>. The payload that we have to send to the service has to be JSON, <code>application/json</code>. And this is why the browser sends an <code>OPTIONS</code> request to the server.</p><p>In web.config the verb <code>OPTIONS</code> is disabled so make sure you have it enabled:</p><pre class="language-xml line-numbers"><code><script>
    <security>
      <requestFiltering>
        <requestLimits maxAllowedContentLength="524288000" />
        <verbs allowUnlisted="true">
          <add verb="OPTIONS" allowed="true" />
          <add verb="PUT" allowed="false" />
          <add verb="DELETE" allowed="false" />
          <add verb="DEBUG" allowed="false" />
        </verbs>
      </requestFiltering>
    </security>
</script></code></pre><p>After making a request we still have an isue:</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/09/405-1.png" class="kg-image"></figure><p>In the early days we could change the global.asax and just add some lines of code to make it work. Nowadays that is not going to help us. We now have to create a pipeline that will support the <code>OPTIONS</code> verb by returning a <code>200 OK</code> status. For this you can use a <code>PreprocessRequestProcessor</code>. The pipeline must execute as soon as possible and preferably be patched as the first processor.</p><p><strong>PreProcess Request and Configuration:</strong></p><pre class="language-csharp line-numbers"><code>
using Sitecore.Pipelines.PreprocessRequest;
using System;
using System.Linq;

namespace Macaw.JSS.Examples
{
    public class CorsOptions : PreprocessRequestProcessor
    {
        public override void Process(PreprocessRequestArgs args)
        {
            var request = args.HttpContext.Request;
            var response = args.HttpContext.Response;
            if (request.Headers.AllKeys.Contains("Origin", StringComparer.InvariantCultureIgnoreCase) &&
                request.RawUrl.StartsWith("/sitecore/api/ssc/auth")
                )
            {
                if(request.HttpMethod == "OPTIONS")
                {
                    response.AddHeader("X-Fabricated-By-Gary", "That's cool");
                    response.AddHeader("Access-Control-Allow-Origin", "*");
                    response.Flush();
                }
                if(request.HttpMethod == "POST")
                {
                    response.AddHeader("Access-Control-Allow-Origin", "*");
                }
            }               
        }
    }
}
</code></pre><pre class="language-xml line-numbers"><code><script>
<configuration xmlns:patch="http://www.sitecore.net/xmlconfig/">
  <sitecore>
    <pipelines>
      <preprocessRequest>
        <processor type="Macaw.JSS.Examples.CorsOptions, Macaw.JSS.Examples"
         patch:before="*[@type='Sitecore.Pipelines.PreprocessRequest.NormalizeRawUrl, Sitecore.Kernel']">
        </processor>
      </preprocessRequest>
    </pipelines>
  </sitecore>
</configuration>
</script></code></pre><p>Make sure to also add the headers for <code>Access-Control-Allow-Headers</code>, <code>Access-Control-Allow-Methods</code> and <code>Access-Control-Allow-Origin</code>. But only the first two can be defined in the web.config. Why?</p><pre class="language-xml line-numbers"><code><script>
    <httpProtocol>
      <customHeaders>
        <remove name="X-Powered-By" />
		<add name="Access-Control-Allow-Headers" value="origin, content-type, accept" />
		<add name="Access-Control-Allow-Methods" value="*" />
      </customHeaders>
    </httpProtocol>
</script></code></pre><p> The JSS API by default will send the <code>Access-Control-Allow-Origin</code> header. Defining it also in the web.config will have JSS send it twice. Browsers don't like it when a header is sent twice and it will break your app. The result is: <strong>Page Not Found</strong>. Ouch!</p><blockquote>Do not define the <code>Access-Control-Allow-Origin</code> header in the web.config as JSS will send the header twice and break your code</blockquote><p>SSC on the other hand also needs the <code>Access-Control-Allow-Origin</code> in place. If not the <code>OPTIONS</code> preflight will fail. This is why the headers are added in the <code>PreprocessRequestProcessor</code>.</p><h3 id="add-ssl-binding-to-iis">Add SSL binding to IIS</h3><p>When posting our login to the SSC we get a 404 telling us the post has to be over <code>HTTPS</code>:</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/09/2018-09-26-09_12_52-Postman.png" class="kg-image"></figure><p>We must assign an SLL binding to our website. Get the script I have here:  <a href="https://pastebin.com/A9CUz1rH">https://pastebin.com/A9CUz1rH</a>. This script will automatically create your certificate, installs it and bind it to your website.</p><p>The following params are needed:</p><ul><li><strong>dnsName</strong> - a name you want to give to your certificate (can be anything you want for localhost)</li><li><strong>siteName</strong> - the website to apply the bindings/cert to (top level, not an application underneath!)</li><li><strong>fqdn</strong> - fully qualified domain name (empty for 'All unassigned', or e.g 'contoso.com')</li></ul><blockquote>If you already have an SSL binding in place, please make sure you remove it otherwise the binding will fail. In Firefox make sure to create the exception for your self-signed certificate.</blockquote><p>Now this all has been completed I'll hit the login button and we get nice responses:</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/09/200ok.png" class="kg-image"></figure>
