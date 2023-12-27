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
  - nodejs
  - reactjs
  - typescript
  - jss
  - cms
  - git
  - asp.net
  - headless
  - mvc
author: garywenneker
paginate: true
---
> Would't it be great if we could sync our Sitecore JSS website to our local JSS dev environment?

You are a hardcore Sitecore developer, or a front end wizard and are starting with Sitecore JSS. At first you might think, I don't know anything about front end stuff, or as a front end dev, you don't want to do anything with Sitecore. Then there's two ways to work with JSS (developer workflows) C[ode First](https://jss.sitecore.com/docs/fundamentals/dev-workflows/code-first) and [Sitecore First](https://jss.sitecore.com/docs/fundamentals/dev-workflows/sitecore-first). And what about the structure? How is data handled? Feeling desperate yet? You can also get into annoying discussions in your company between front end developers and Sitecore developers of what would be the best way to go.

### A proven approach

Here at [Macaw](https://www.macaw.nl/), we've been utilizing [React](https://reactjs.net/) with SSR (for [SEO](https://en.wikipedia.org/wiki/Search_engine_optimization) purposes) that consumes [JSON](https://en.wikipedia.org/wiki/JSON) from [MVC controllers](https://en.wikipedia.org/wiki/ASP.NET_MVC) for a long time now. Front end developers work closely together with the Sitecore devs. All logic is provided through the ASP.NET MVC logic to the front end application which parses all data and transforms it to HTML. This is done server side (SSR) and for client interaction it also will hydrate. JSON data from Sitecore outputted from the controller is pulled into a file allowing the front end devs to use the mock data in their apps. This is not an ideal situation but with the connected mode in JSS they now can use real live data.

With the introduction of Sitecore JSS we also had some discussion which developer workflow should be used. The front end developers wanted to go with the code first approach as the Sitecore developers wanted to start with defining the templates because of inheritance and other reasons: the Sitecore first approach.

In the end, the Sitecore first approach is the only way because we cannot run JSS with Sitecore in disconnected mode all the time. And also because this would make JSS just another static site generator. You can imagine these discussions can be hard. So I had to make an end of that :-)

### Umbrella for Sitecore JSS

From JSS it is possible to define a routing structure, define manifests for metadata like templates and components etc., add data for layouts and push them all using the JSS CLI to your Sitecore server. But what about going from Sitecore to your local offline JSS environment? No mechanism allows you to do that. Wouldn't it be cool if you could export your JSS website and pull it in a way that it would create the manifest files, routing and content? And even download images and files from the media library and assign them in your YML files?

Umbrella for Sitecore JSS is a utility that synchronizes items from your Sitecore JSS website to a folder on your computer where you can work directly in your local JSS application even when you're offline. It pulls the current state of your app in Sitecore to your local state.

**Why?** In Sitecore JSS you can choose between two developer workflow states. I think `Sitecore First` is the one to go with. If your website is running in the production environment you have to. But, you always start with the `Code First` approach.

Umbrella for Sitecore JSS consists of two parts, the [Umbrella endpoint](https://github.com/macaw-interactive/umbrella-for-sitecore-jss) and the [Umbrella NodeJS script](https://github.com/macaw-interactive/react-jss-typescript-starter/blob/develop/scripts/umbrella.js). First you need to install the Umbrellea endpoint. You can do this in two ways:

*   **Install the solution** - [The solution](https://github.com/macaw-interactive/umbrella-for-sitecore-jss) is set up using the [Helix templates by Anders Laub](https://github.com/LaubPlusCo/helix-msbuild-example) and I added [Cake](https://cakebuild.net/) build scripts, which are very very cool, to provide clean build and deploy steps for your environment. Check out the releases [here](https://github.com/macaw-interactive/umbrella-for-sitecore-jss/releases).

![](/assets/img/uploads/2019/05/2019-05-03-11_12_57-Umbrella.PanTau---Microsoft-Visual-Studio---Administrator--1.png)

*   **Use the NuGet feed** - I also provided two [NuGet packages](https://www.nuget.org/packages/Umbrella.PanTau/) for Sitecore versions 9.1 and 9.1.1\. at the moment of this writing, but I'll keep updating it when other versions arrive.  

![](/assets/img/uploads/2019/05/2019-05-03-11_09_03-NuGet-Gallery-_-Umbrella.PanTau-9.1.1.1.png)

### Umbrella.PanTau

The Umbrella.PanTau project adds an extra JSS endpoint: `/sitecore/api/layout/render/umbrella` to your Sitecore environment. This endpoint is used by the [Umbrella Sync Script](https://github.com/macaw-interactive/react-jss-typescript-starter/blob/develop/scripts/umbrella.js) to extract data from your Sitecore JSS environment. The output below is an example:

{% highlight json %}
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
{% endhighlight %}

## Umbrella Sync Script

The [Umbrella Sync Script](https://github.com/macaw-interactive/react-jss-typescript-starter/blob/develop/scripts/umbrella.js) is a NodeJS script that has to be put into a folder named scripts. It has to be executed from the root of your JSS project folder:

`node .\scripts\umbrella.js`

![](/assets/img/uploads/2019/05/2019-05-03-11_17_58-index.js---react-jss-typescript-starter---Visual-Studio-Code--Administrator-.png)

At the moment of this writing the Umbrella Sync Script is part of the [React TypeScript Starter](https://github.com/macaw-interactive/react-jss-typescript-starter/tree/develop/scripts) which is part of the Umbrella project. Soon I'll be writing some more about this awesome great JSS starter.

> Use Umbrella for Sitecore JSS in combination with the ultimate React TypeScript Starter for Sitecore JSS

### Routing

JSS uses dynamic routing based on the [Layout Service](https://jss.sitecore.com/docs/fundamentals/services/layout-service) (or local route data files in _disconnected mode_), and uses route/navigation changes to trigger app state changes. When we want to sync we also must have some mechanism in place which holds the routes independently from the Layout Service or the local route data files.  
Querying the JSS endpoint only will give you one route. From the client app we don't know the actual routes which are created in Sitecore. This is why, at the moment, we must add extra route information.

To overcome the problem of routing in the case described above you should create a file named `routeconfig.json` in the root of your JSS client app and add the routes you have:

{% highlight json %}
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
{% endhighlight %}

### Usage

`node .\scripts\umbrella.js sync`

### Options

<table>

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

## API

The Umbrella endpoint will output data that is used by the Umbrella NodeJS script. The Umbrella NodeJS script will export this data to several sections in your local JSS application. Note that templates, renderings, media items are all inside a project folder. Umbrella will only export items from these folders, which means that foundation and feature folders are not processed (at the moment).

### Placeholders

Exports all available placeholders from your JSS Sitecore environment to `<app root>/sitecore/definitions/placeholders.sitecore.js`.

### Templates

Exports all available templates from your JSS Sitecore environment to `<app root>/sitecore/definitions/templates`.

### Components

Exports all available components from your JSS Sitecore environment to `<app root>/sitecore/definitions/components`.

### Content and Media

This will export the content of your routes (pages) to your local JSS development environment and saves it in the `<app root>/data/routes` folder. During the export the script will check for image fields and wil save them to the `<app root>/data/media folder`.

### Input

If you have any suggestions, ideas want to participate find me on Slack!