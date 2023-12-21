---
layout: post
title: 'Sitecore JSS: Umbrella'
description: >-
  Umbrella for Sitecore JSS is a utility that synchronizes items from your Sitecore JSS website to a folder on your computer where you can work directly in your local JSS application even when you&#x27;re offline.
date: 2019-05-03 08:21:12.000000000 +01:00
image: >-
  /assets/img/uploads/umbrella.png
optimized_image: >-
  /assets/img/uploads/umbrella.png
category: blog
tags:
  - sitecore
  - react
author: garywenneker
paginate: true
---
<blockquote>Would't it be great if we could sync our Sitecore JSS website to our local JSS dev environment?</blockquote><p>You are a hardcore Sitecore developer, or a front end wizard and are starting with Sitecore JSS. At first you might think, I don't know anything about front end stuff, or as a front end dev, you don't want to do anything with Sitecore. Then there's two ways to work with JSS (developer workflows) C<a href="https://jss.sitecore.com/docs/fundamentals/dev-workflows/code-first">ode First</a> and <a href="https://jss.sitecore.com/docs/fundamentals/dev-workflows/sitecore-first">Sitecore First</a>. And what about the structure? How is data handled? Feeling desperate yet? You can also get into annoying discussions in your company between front end developers and Sitecore developers of what would be the best way to go.</p><h3 id="a-proven-approach">A proven approach</h3><p>Here at <a href="https://www.macaw.nl/">Macaw</a>, we've been utilizing <a href="https://reactjs.net/">React</a> with SSR (for <a href="https://en.wikipedia.org/wiki/Search_engine_optimization">SEO</a> purposes) that consumes <a href="https://en.wikipedia.org/wiki/JSON">JSON</a> from <a href="https://en.wikipedia.org/wiki/ASP.NET_MVC">MVC controllers</a> for a long time now. Front end developers work closely together with the Sitecore devs. All logic is provided through the ASP.NET MVC logic to the front end application which parses all data and transforms it to HTML. This is done server side (SSR) and for client interaction it also will hydrate. JSON data from Sitecore outputted from the controller is pulled into a file allowing the front end devs to use the mock data in their apps. This is not an ideal situation but with the connected mode in JSS they now can use real live data.</p><p>With the introduction of Sitecore JSS we also had some discussion which developer workflow should be used. The front end developers wanted to go with the code first approach as the Sitecore developers wanted to start with defining the templates because of inheritance and other reasons: the Sitecore first approach. </p><p>In the end, the Sitecore first approach is the only way because we cannot run JSS with Sitecore in disconnected mode all the time. And also because this would make JSS just another static site generator. You can imagine these discussions can be hard. So I had to make an end of that :-)</p><h3 id="umbrella-for-sitecore-jss">Umbrella for Sitecore JSS</h3><p>From JSS it is possible to define a routing structure, define manifests for metadata like templates and components etc., add data for layouts and push them all using the JSS CLI to your Sitecore server. But what about going from Sitecore to your local offline JSS environment? No mechanism allows you to do that. Wouldn't it be cool if you could export your JSS website and pull it in a way that it would create the manifest files, routing and content? And even download images and files from the media library and assign them in your YML files?</p><p>Umbrella for Sitecore JSS is a utility that synchronizes items from your Sitecore JSS website to a folder on your computer where you can work directly in your local JSS application even when you're offline. It pulls the current state of your app in Sitecore to your local state. </p><p><strong>Why?</strong> In Sitecore JSS you can choose between two developer workflow states. I think <code>Sitecore First</code> is the one to go with. If your website is running in the production environment you have to. But, you always start with the <code>Code First</code> approach.</p><p>Umbrella for Sitecore JSS consists of two parts, the <a href="https://github.com/macaw-interactive/umbrella-for-sitecore-jss">Umbrella endpoint</a> and the <a href="https://github.com/macaw-interactive/react-jss-typescript-starter/blob/develop/scripts/umbrella.js">Umbrella NodeJS script</a>. First you need to install the Umbrellea endpoint. You can do this in two ways:</p><ul><li><strong>Install the solution </strong>- <a href="https://github.com/macaw-interactive/umbrella-for-sitecore-jss">The solution</a> is set up using the <a href="https://github.com/LaubPlusCo/helix-msbuild-example">Helix templates by Anders Laub</a> and I added <a href="https://cakebuild.net/">Cake</a> build scripts, which are very very cool, to provide clean build and deploy steps for your environment. Check out the releases <a href="https://github.com/macaw-interactive/umbrella-for-sitecore-jss/releases">here</a>.</li></ul><figure class="kg-image-card kg-width-full"><img src="/content/images/2019/05/2019-05-03-11_12_57-Umbrella.PanTau---Microsoft-Visual-Studio---Administrator--1.png" class="kg-image"></figure><ul><li><strong>Use the NuGet feed</strong> - I also provided two <a href="https://www.nuget.org/packages/Umbrella.PanTau/">NuGet packages</a> for Sitecore versions 9.1 and 9.1.1. at the moment of this writing, but I'll keep updating it when other versions arrive.<br></li></ul><figure class="kg-image-card kg-width-full"><img src="/content/images/2019/05/2019-05-03-11_09_03-NuGet-Gallery-_-Umbrella.PanTau-9.1.1.1.png" class="kg-image"></figure><p></p><h3 id="umbrella-pantau">Umbrella.PanTau</h3><p>The Umbrella.PanTau project adds an extra JSS endpoint: <code>/sitecore/api/layout/render/umbrella</code> to your Sitecore environment. This endpoint is used by the <a href="https://github.com/macaw-interactive/react-jss-typescript-starter/blob/develop/scripts/umbrella.js">Umbrella Sync Script</a> to extract data from your Sitecore JSS environment. The output below is an example:</p><pre><code class="line-numbers language-javascript"><script type="text/plain">
{
  "sitecore": {
    "context": {
      "visitorIdentificationTimestamp": 636889392519235300,
      "pageEditing": false,
      "site": {
        "name": "react-jss-typescript-starter"
      },
      "pageState": "normal",
      "language": "en",
      "placeholders": [
        {
          "id": "4cd956d4-a36e-5c78-ab14-3b56f66cb503",
          "name": "footer-address",
          "displayName": "footer-address"
        },
        {
          "id": "c23b835f-a290-5b95-b5d5-e2dbf3ca499e",
          "name": "jss-main",
          "displayName": "Main"
        }
      ],
      "templates": [
        {
          "BaseIDs": [
            "b36ba9fd-0dc0-49c8-bea2-e55d70e6af28"
          ],
          "CustomValues": null,
          "FullName": "Project/react-jss-typescript-starter/App Route",
          "Icon": "Apps/16x16/routes.png",
          "ID": "4e63ea9e-0174-505b-9e6e-82136191959e",
          "Name": "App Route",
          "StandardValueHolderId": "4c235921-3a2f-5f3e-bb91-fc62415c88cf",
          "fields": [
            {
              "id": "d10dc31e-cf38-567f-98d8-655fee342a54",
              "name": "pageTitle",
              "defaultValue": "",
              "type": "Single-Line Text",
              "typeKey": "single-line text",
              "icon": "",
              "isShared": false,
              "inherited": false,
              "templateId": "4e63ea9e-0174-505b-9e6e-82136191959e",
              "templateName": "App Route"
            }
          ]
        }
      ],
      "renderings": [
        {
          "id": "08f9daac-db92-575a-873b-b53cce7de290",
          "name": "ContentBlock",
          "displayName": "Content Block",
          "icon": "Office/16x16/document_tag.png",
          "fields": [
            {
              "id": "037fe404-dd19-4bf7-8e30-4dadf68b27b0",
              "name": "componentName",
              "type": "CommonFieldTypes.SingleLineText"
            }
          ],
          "placeholders": [
            "{C23B835F-A290-5B95-B5D5-E2DBF3CA499E}|jss-main",
            "{11448A04-F955-5B19-A54A-9E0EC07641AF}|jss-reuse-example"
          ]
        }
      ]
    },
    "route": {
      "name": "styleguide",
      "displayName": "styleguide",
      "fields": {
        "pageTitle": {
          "value": "Styleguide | Sitecore JSS"
        }
      },
      "databaseName": "master",
      "deviceId": "fe5d7fdf-89c0-4d99-9aa3-b5fbd009c9f3",
      "itemId": "ed4692a0-7439-59c4-8451-7a55c061158c",
      "itemLanguage": "en",
      "itemVersion": 1,
      "layoutId": "1e09f9fe-4092-5183-9bd2-6a75c1815c59",
      "templateId": "4e63ea9e-0174-505b-9e6e-82136191959e",
      "templateName": "App Route",
      "placeholders": {
        "jss-main": [
        ]
      }
    }
  }
}
</script></code></pre><p></p><h2 id="umbrella-sync-script">Umbrella Sync Script</h2><p>The <a href="https://github.com/macaw-interactive/react-jss-typescript-starter/blob/develop/scripts/umbrella.js">Umbrella Sync Script</a> is a NodeJS script that has to be put into a folder named scripts. It has to be executed from the root of your JSS project folder:</p><p><code>node .\scripts\umbrella.js</code></p><figure class="kg-image-card kg-width-full"><img src="/content/images/2019/05/2019-05-03-11_17_58-index.js---react-jss-typescript-starter---Visual-Studio-Code--Administrator-.png" class="kg-image"></figure><p>At the moment of this writing the Umbrella Sync Script is part of the <a href="https://github.com/macaw-interactive/react-jss-typescript-starter/tree/develop/scripts">React TypeScript Starter</a> which is part of the Umbrella project. Soon I'll be writing some more about this awesome great JSS starter.</p><blockquote>Use Umbrella for Sitecore JSS in combination with the ultimate React TypeScript Starter for Sitecore JSS</blockquote><h3 id="routing">Routing</h3><p>JSS uses dynamic routing based on the <a href="https://jss.sitecore.com/docs/fundamentals/services/layout-service">Layout Service</a> (or local route data files in <em>disconnected mode</em>), and uses route/navigation changes to trigger app state changes. When we want to sync we also must have some mechanism in place which holds the routes independently from the Layout Service or the local route data files.<br>Querying the JSS endpoint only will give you one route. From the client app we don't know the actual routes which are created in Sitecore. This is why, at the moment, we must add extra route information.</p><p>To overcome the problem of routing in the case described above you should create a file named <code>routeconfig.json</code> in the root of your JSS client app and add the routes you have:</p><pre><code class="line-numbers language-javascript"><script type="text/plain">
{
    "path": "/",
    "lang": [
        "en"
    ],
    "routes": [
        {
            "path": "umbrella",
            "lang": [
                "en"
            ]
        },
        {
            "path": "scaffold",
            "lang": [
                "en"
            ]
        },
        {
            "path": "styleguide",
            "lang": [
                "en"
            ]
        },
        {
            "path": "graphql",
            "lang": [
                "en"
            ],
            "routes": [
                {
                    "path": "sample-1",
                    "lang": [
                        "en"
                    ]
                },
                {
                    "path": "sample-2",
                    "lang": [
                        "en"
                    ]
                }
            ]
        },
        {
            "path": "styleguide",
            "lang": [
                "en"
            ],
            "routes": [
                {
                    "path": "custom-route-type",
                    "lang": [
                        "en"
                    ]
                }
            ]
        }
    ]
}
</script></code></pre><h3 id="usage">Usage</h3><p><code>node .\scripts\umbrella.js sync</code></p><h3 id="options">Options</h3><table>
<thead>
<tr>
<th>Switch</th>
<th>Action</th>
</tr>
</thead>
<tbody>
<tr>
<td>-t, --templates</td>
<td>Sync all the templates from Sitecore</td>
</tr>
<tr>
<td>-p, --placeholders</td>
<td>Sync all the placeholders from Sitecore</td>
</tr>
<tr>
<td>-m, --manifests</td>
<td>Sync all the component manifests from Sitecore</td>
</tr>
<tr>
<td>-c, --content</td>
<td>Sync all the content from your Sitecore JSS website</td>
</tr>
</tbody>
</table>
<p></p><p></p><h2 id="api">API</h2>
<p>The Umbrella endpoint will output data that is used by the Umbrella NodeJS script. The Umbrella NodeJS script will export this data to several sections in your local JSS application. Note that templates, renderings, media items are all inside a project folder. Umbrella will only export items from these folders, which means that foundation and feature folders are not processed (at the moment).</p>
<h3 id="placeholders">Placeholders</h3>
<p>Exports all available placeholders from your JSS Sitecore environment to <code>&lt;app root&gt;/sitecore/definitions/placeholders.sitecore.js</code>.</p>
<h3 id="templates">Templates</h3>
<p>Exports all available templates from your JSS Sitecore environment to <code>&lt;app root&gt;/sitecore/definitions/templates</code>.</p>
<h3 id="components">Components</h3>
<p>Exports all available components from your JSS Sitecore environment to <code>&lt;app root&gt;/sitecore/definitions/components</code>.</p>
<h3 id="contentandmedia">Content and Media</h3>
<p>This will export the content of your routes (pages) to your local JSS development environment and saves it in the <code>&lt;app root&gt;/data/routes</code> folder. During the export the script will check for image fields and wil save them to the <code>&lt;app root&gt;/data/media folder</code>.</p>
<p></p><h3 id="input">Input</h3><p>If you have any suggestions, ideas want to participate find me on Slack!</p>
