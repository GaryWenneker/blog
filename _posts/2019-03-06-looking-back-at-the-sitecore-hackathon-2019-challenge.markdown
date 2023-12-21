---
layout: post
title: 'Looking back at the Sitecore Hackathon 2019 challenge: DinoCore'
date: 2019-03-06 00:04:12.000000000 +01:00
description: >-
  While being scared a little if I could handle the whole 24 hours I started this great adventure with Jean-François &quot;Jeff&quot; L&#x27;Heureux (Canada) and Wessel &#x27;t Hoen (the Netherlands). Before the Hackathon even began we had meetings to think about directions and possible things we wanted to build.
image: >-
  /assets/img/uploads/screenshot3.png
optimized_image: >-
  /assets/img/uploads/screenshot3.png
category: blog
tags:
  - sitecore
  - hackathon
  - nodejs
  - reactjs
  - typescript
  - jss
  - cms
  - graphql
  - design
  - git
  - vsts
  - css
  - flexbox
  - asp.net
author: garywenneker
paginate: true
---
<p>While being scared a little if I could handle the whole 24 hours I started this great adventure with <a href="https://ca.linkedin.com/in/jflheureux">Jean-François "Jeff" L'Heureux</a> (Canada) and <a href="https://nl.linkedin.com/in/wessel-t-hoen-76818519">Wessel 't Hoen</a> (the Netherlands). Before the <a href="http://www.sitecorehackathon.org/sitecore-hackathon-2019/">Hackathon</a> even began we had meetings to think about directions and possible things we wanted to build.  But the judges made the contest harder. This year they added additional requirements! :-)</p><p></p><h2 id="starting-with-an-idea">Starting with an idea</h2><p>And so our ideas were flushed down the drain and we started thinking about something new. We chose the JSS way. Functionality that would change the presentation of our component based on our, or someone elses behavior. For this we decided to go with the <a href="https://chromedino.com/">Google Dinosaur Game</a>. The way to go was hosting it in Sitecore's JavaScript Services.</p><p></p><h2 id="setting-up-an-outline">Setting up an outline</h2><p>For our application we came up with the following technical items we wanted to add:</p><ul>
<li>Sitecore JSS components hosting React components</li>
<li>a GraphQL client API for handling data back and forth connecting to Sitecore</li>
<li>GraphQL services integrated in Sitecore with one root schema containing logic for both querying and doing mutations.</li>
<li>Storing highscores as items in Sitecore via mutations in GraphQL</li>
<li>Retrieving highscores through Sitecore search via GraphQL</li>
<li>Showing real time high score stats</li>
</ul>
<p></p><h2 id="building-the-app">Building the app</h2><p>I must say, Jeff had the hardest time of us all. The <a href="https://www.quora.com/Why-are-the-French-called-frogs">Froggy</a> had to start right after a day work. For us <a href="https://www.urbandictionary.com/define.php?term=Kaaskop">Cheese Heads</a> it was much easier, starting at 2 AM <a href="https://time.is/nl/CET">CET</a> we could have a good nap before starting the 24 hour endeavour. We were armed to the teeth. Espresso, Red bull (original and orange), juices, bourbon and <a href="https://en.wikipedia.org/wiki/Islay_whisky">Islay single malt whisky</a>. The food was good too. Meat balls from the oven, tosties (<a href="https://en.wikipedia.org/wiki/Croque_monsieur">croque monsier</a>), streak, tapas, candy (am glad this happens only once a year).</p><p>It was fun building the app. Our combined skills resulted in a great app we can be proud of. If you would like to test it out, the code is available <a href="https://github.com/Sitecore-Hackathon/2019-Cheese-n-Frog"><strong>here</strong></a>.</p><p></p><h2 id="having-fun-during-the-24-hour-gig">Having fun during the 24 hour gig</h2><p>During the Hackathon we made a couple of pics to share with the world :-)</p><figure class="kg-image-card kg-width-wide"><img src="/assets/img/uploads/2019/03/53454921_2319498851670944_6032756551960231936_n.jpg" class="kg-image"><figcaption>setting up our workplace wih my little white hacker</figcaption></figure><figure class="kg-image-card kg-width-wide"><img src="/assets/img/uploads/2019/03/53238228_432039194216075_810252181755133952_n.jpg" class="kg-image"><figcaption>Team Cheese n Frog</figcaption></figure><figure class="kg-image-card kg-width-wide"><img src="/assets/img/uploads/2019/03/53886057_1009188145957078_1519079277750910976_n.jpg" class="kg-image"><figcaption>Midnight snacks</figcaption></figure><figure class="kg-image-card kg-width-wide"><img src="/assets/img/uploads/2019/03/53313391_256546531902355_3141501507367600128_n.jpg" class="kg-image"><figcaption>Light beverages at noon ;-)</figcaption></figure><p></p><h2 id="finalizing-everything">Finalizing everything</h2><p>The end was extremely hard. It felt like I had been run over by a big truck. After 24 hours it was hard to think clearly but it was important not to make any mistakes. Two minutes before closing time we pushed the last pull request to the master branch. This was the moment we could finally sit back and relax. </p><p></p><h2 id="the-win">The win</h2><p>So did we win? I think we did. How? Creating new friendships, learning to work in a team without knowing each other's way of working. I learned a lot and was happy also to share my knowledge. I could even add some nice new things to my default toolset.</p><p>Of course we raised our glasses and looked back at a great successful Hackathon.</p><figure class="kg-image-card kg-width-full"><img src="/assets/img/uploads/2019/03/D0sjXzQWsAIjZVI--1-.jpg" class="kg-image"><figcaption>Celebrating the good end</figcaption></figure><p></p>
