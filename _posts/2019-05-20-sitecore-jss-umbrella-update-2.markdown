---
layout: post
title: 'Sitecore JSS: Umbrella Update 2'
description: >-
  A few weeks ago I released Umbrella for Sitecore JSS, a utility that synchronizes items from your Sitecore JSS website to your local JSS development environment. Behind the scenes I&#x27;ve been working very hard to add new features as you can read below.Use Umbrella globallyUmbrella is now available from
date: 2019-05-20 08:21:12.000000000 +01:00
image: >-
  /assets/img/uploads/31936071243_9555aea759_k.jpg
optimized_image: >-
  /assets/img/uploads/31936071243_9555aea759_k.jpg
category: blog
tags:
  - sitecore
  - react
author: garywenneker
paginate: true
---
<p>A few weeks ago I released <a href="https://gary.wenneker.org/umbrella-for-sitecore-jss/">Umbrella for Sitecore JSS</a>, a utility that synchronizes items from your Sitecore JSS website to your local <a href="https://jss.sitecore.com/">JSS development environment</a>. Behind the scenes I've been working very hard to add new features as you can read below.</p><h3 id="use-umbrella-globally">Use Umbrella globally</h3><figure class="kg-image-card kg-width-full"><img src="/content/images/2019/05/image-1.png" class="kg-image"></figure><p><a href="https://github.com/GaryWenneker/jss-umbrella">Umbrella</a> is now available from <a href="https://www.npmjs.com/package/jss-umbrella">NPM</a>! The CLI can be used globally. Install Umbrella using the command <code>npm link jss-umbrella</code>. There is no reason to add the script locally in your project anymore. Use the same command to update the CLI. For the scripts to work you must install <a href="https://github.com/GaryWenneker/Umbrella.PanTau">Umbrella.Pantau</a>, the Umbrella JSS endpoint. Make sure you have the files <code>scjssconfig.json</code> and Umbrella's <code>routeconfig.json</code> in the directory you are executing the CLI from.</p><h3 id="typescript-templates">Typescript Templates</h3><p>As of now you can output the generated manifests (placeholders, components and templates) as TypeScript files which comes in handy when you are using the <a href="https://github.com/macaw-interactive/umbrella-for-sitecore-jss">Unofficial Sitecore JSS starter</a>. Just use the <strong><code>--typescript</code></strong> switch.</p><h3 id="dryrun-switch">Dryrun Switch</h3><p>If you want to test that your project configuration is set up correctly, you'll find the <code>--dryrun</code> switch useful. While the script is executed as usual the files are not written to your local disk.</p><h3 id="nuget-feed-updated">NuGet Feed Updated</h3><figure class="kg-image-card kg-width-full"><img src="/content/images/2019/05/image.png" class="kg-image"></figure><p>Also the <a href="https://www.nuget.org/packages/Umbrella.PanTau/9.1.1.3">server side package</a> is updated. Small changes and fixes are done. Add the package to your Sitecore project to be able to use the Umbrella endpoint. With loads of ideas I started updating the scripts. You can also pull the sources from <a href="https://github.com/GaryWenneker/Umbrella.PanTau">GitHub</a> if you want.</p><h2 id="examples">Examples</h2><p>Help is shown when you run <code>umbrella -h</code> or <code>umbrella --help</code></p><p>Sync placeholders:</p><p><code>umbrella -p </code></p><p>Sync templates and write it in TypeScript:</p><p><code>umbrella -t -x</code></p><p>Sync component manifests and write it in TypeScript but do not write to disk:</p><p><code>umbrella -t -x -d</code></p>
