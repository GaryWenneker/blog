---
layout: post
title: Supercharging Sitecore with SXA
date: 1970-01-18 14:15:32.000000000 +01:00
---
<p>One of the most interesting discussions I had the last couple of weeks is when to use custom build solutions, maybe a boilerplate or Sitecore Experience Accelerator (in short <a href="https://doc.sitecore.net/sitecore_experience_accelerator">SXA</a>.  In my <a href="http://gary.wenneker.org/2018/01/04/sitecore-experience-accelerator/">previous post</a> I wrote about the feeling one can have when playing around with SXA, how I could use my own (React) components in SXA, but there is so much more under the hood. </p><p>In this post I will zoom in more into the world of SXA and try to convince you that SXA is the way to go.</p><p><em>Want to skip to the <a>conclusion</a>?</em></p><h2 id="choosing-a-boilerplate-over-sxa">Choosing a Boilerplate over SXA</h2><figure class="kg-image-card"><img src="https://i.giphy.com/aEL3MsCoqelsQ.gif" class="kg-image"></figure><p><br>Over the years many companies discovered a way of introducing functionality and logic that were added to Sitecore: a framework or "boilerplate", to streamline development, enabling continuous delivery, governance etc etc. With this proven approach customers recognized a quicker time to market and therefore additional marketing campaigns were no budgetary issue because there was more money left to spend. One of the strangest things I have seen in the past is that SXA is seen as a fixed framework with its own set of components and fixed way of working and that's why some organisations will not even think of using SXA.</p><h2 id="choosing-sxa-over-other-frameworks">Choosing SXA over other frameworks</h2><p>An different question is whether one should abandon the boilerplate and focusing primarily on Sitecore Experience Accelerator. SXA out of the box will provide a lot of extra's like:</p><ul><li>The new and improved <a href="https://doc.sitecore.net/sitecore_experience_accelerator/setting_up_and_configuring/setting_up/set_sxa_caching_options">caching framework</a> <em><strong>AWESOME!</strong></em></li><li>Virtually using <a href="https://doc.sitecore.net/sitecore_experience_accelerator/building_the_layout/structure/the_grid_settings">any grid</a> you want to use, or the grids that come <a href="https://doc.sitecore.net/sitecore_experience_accelerator/getting_started_with_sxa/layout/the_grid_layout">out of the box</a> (Foundation, Grid960, Bootstrap).</li><li>Sharing content and presentation between sites</li><li>Scaffolding and encapsulating of controls (as mentioned in my former post)</li><li>A strict structure enforcing governance</li><li>SXA <a href="https://doc.sitecore.net/sitecore_experience_accelerator/data_modeling/json/introducing_sxa_data_modeling">JSON data modeling</a>, providing a JSON API to access the SXA content.</li><li>Complete liberty in managing page designs and partial designs</li></ul><p>Going this way will actually boost your time to market and provide you with a site in no time. Many things are available from the front-end where in the past one would need developers to make changes or add functionality. This problem has been removed because SXA has a mandatory dependency to Sitecore PowerShell Extensions. Creating a site has never been easier. A set of PowerShell scripts are executed under the hood doing all kinds of things to scaffold the entire structure for you. When done you can start setting up your pages right away without any pain.</p><h2 id="sxa-the-hybrid-way">SXA the Hybrid way</h2><figure class="kg-image-card"><img src="https://i.giphy.com/fIyO88gQ9uGGc.gif" class="kg-image"></figure><p><br>My view is simple: I think that one should not abandon any framework, boilerplate in any way when it comes to logic and functionality that is not provided by SXA. Best of both worlds often might give the best results. As stated earlier a boilerplate often also dictates the flow of development (<a href="https://helix.sitecore.net/">Helix</a>) and in the end will speed up the process taking in account all other processes.</p><p>Using only SXA is in my opinion not the right way because when it comes to <a href="https://bradfrost.com/blog/post/atomic-web-design/">Atomic Design</a> you do now want a content editor to start scaffolding a web page using atoms and molecules, even if it is a button. Rather use a rendering variant to solve presentation issues.</p><p>A small comparison:</p><table width="100%" style="font-size:.8em;padding:1em;">
<thead style="background-color: #f6f8fa;">
<tr>
<th></th>
<th align="left">SXA</th>
<th align="left">Boilerplate</th>
</tr>
</thead>
<tbody>
<tr>
<td style="font-weight:normal;font-style:italic;">Redactional</td>
<td align="left">SXA component toolbox</td>
<td align="left">Default</td>
</tr>
<tr>
<td style="font-weight:normal;font-style:italic;">Content</td>
<td align="left">Helix</td>
<td align="left">Helix (if implemented)</td>
</tr>
<tr>
<td style="font-weight:normal;font-style:italic;">Design</td>
<td align="left">If only SXA then it could get rigid</td>
<td align="left">Full freedom, clean HTML</td>
</tr>
<tr>
<td style="font-weight:normal;font-style:italic;">Marketing</td>
<td align="left">Short TTM, no dev required</td>
<td align="left">Relative short TTM, dev required</td>
</tr>
<tr>
<td style="font-weight:normal;font-style:italic;">Governance/Admin/Update</td>
<td align="left">All new logic is provided when a new update arrives</td>
<td align="left">Maintain by yourself</td>
</tr>
<tr>
<td style="font-weight:normal;font-style:italic;">Commerce</td>
<td align="left">OOTB</td>
<td align="left"></td>
</tr>
</tbody>
</table><p>The mix of your own boilerplate with SXA will supercharge your process and combine speed and ease. SXA has a lot of great features most boilerplates don't have which makes to combination of the two so special.</p><p>SXA is here to stay and I am sure we'll see more and more in the near future (think Commerce 9). I am very curious of other peoples views on the subject so I hope this post is a nice start for a discussion :-)</p>
