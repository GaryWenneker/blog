---
layout: post
title: 'Sitecore JSS: Triggering Marketing Events'
description: >-
  Trigger marketing events from Sitecore JSS and track visitors during current and past web sessions for personalization, reporting and optimizing your website.
date: 2019-02-03 08:21:12.000000000 +01:00
image: >-
  https://images.unsplash.com/photo-1535017614657-1c6f308729b8?ixlib&#x3D;rb-1.2.1&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;h&#x3D;721&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ
optimized_image: >-
  https://images.unsplash.com/photo-1535017614657-1c6f308729b8?ixlib&#x3D;rb-1.2.1&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;h&#x3D;721&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ
category: blog
tags:
  - sitecore
  - react
author: garywenneker
paginate: true
---
---
<p>With Sitecore JSS the development strategy has shifted. It enables frontend developers to participate in Sitecore development teams, whether you are creating a single page application (SPA) or consuming the JSS layout service providing you with the context of the route. User interaction is done on the client side which offers us certain advantages and challenges. One of the advantages we get is that we can trigger marketing events from the frontend with just a few lines of code.</p><p>The following steps will show you how to set up your Sitecore and frontend environment and how to trigger an event or marketing goal from your frontend code.</p><hr><h3 id="deploying-the-marketing-definitions">Deploying the marketing definitions</h3><p>Before we're able to trigger an event we have to deploy the marketing definitions. This is done from the control panel <em>(Dashboard -&gt; Control Panel)</em>. Click on the <strong><em>Deploy marketing definitions</em></strong> link. This will open a window with all the marketing definitions. Select them all and click <strong><em>Deploy</em></strong>. This might take about 15 minutes to run so don't think your system renders unresponsive and please don't be impatient :-)</p><figure class="kg-image-card"><img src="/content/images/2019/02/2019-02-03-08_56_36-Control-Panel.png" class="kg-image"></figure><p></p><h3 id="setting-up-custom-goals-and-events">Setting up custom goals and events</h3><p>From JSS we can track several items:</p><ul>
<li><strong>Goal</strong> - Goals are activities that visitors can perform on your website. You create goals to track and measure how visitors engage with the website and campaigns – both online and offline.</li>
<li><strong>Outcome</strong> - An outcome is the business significant result of a dialog between a contact and a brand. Through a contact's path to becoming a customer, you can track the events they have triggered and the goals they have converted on their journey. This provides you with an insight into how contacts interact with your website, as well as the relative financial value they have for your organization.</li>
<li><strong>Event</strong> - Events track visitor activity on a website. Tracking events helps build up a more complete picture of a visitor’s behavior as they navigate your website. You should assign engagement value points to all events to reflect their relative importance to your organization.</li>
<li><strong>PageView</strong> - Each time a page is viewed, an event is triggered.</li>
<li><strong>Campaign</strong> - The campaign which can be triggered.</li>
</ul>
<p>Now we have set up the marketing definitions we can start creating a marketing event. On the dashboard under <em>Marketing Applications</em> click on <em>Marketing Control Panel</em>. This will bring up a content editor interface. Right-click the <em>Goals </em>node, click on <em>Insert </em>and select <em>Goal</em>. Give it a name you want.</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2019/02/goal.png" class="kg-image"></figure><p>In the content window expand the <em>Data </em>section and add a value to the <em>points </em>field. Very important for you to know that this goal is not yet deployed! To do so, click on the <em>Review </em>tab and hit the <em>Deploy </em>button. Now we can dive into the code on the client side.</p><p></p><h3 id="sitecore-jss-tracking-api">Sitecore JSS Tracking API</h3><p>The Tracking API can track several things by making a request to the Sitecore Layout Service. It accepts an array of specific types (with their properties):</p><ul>
<li><strong>Campaign</strong>
<ul>
<li><em>campaignId</em>: <code>string</code></li>
</ul>
</li>
<li><strong>Goal</strong>
<ul>
<li><em>goalId</em>: <code>string</code></li>
</ul>
</li>
<li><strong>Event</strong>
<ul>
<li><em>eventId</em>: <code>string</code></li>
</ul>
</li>
<li><strong>Outcome</strong>
<ul>
<li><em>outcomeId</em>: string</li>
<li><em>currencyCode</em>: <code>[nullable]</code> <code>string</code></li>
<li><em>monetaryValue</em>: <code>[nullable]</code> <code>number</code></li>
</ul>
</li>
<li><strong>PageView</strong>
<ul>
<li><em>pageId</em>: <code>string</code></li>
<li><em>url</em>: <code>string</code></li>
</ul>
</li>
</ul>
<p>When we observe the definition for the Tracking API we'll see that it accepts an array:</p><pre><code class="line-numbers language-javascript"><script type="text/plain">
export declare function trackEvent(events: Array<EventInstance | GoalInstance | OutcomeInstance | CampaignInstance | PageViewInstance>, options: TrackingRequestOptions): Promise<void>;
</script></code></pre><p>Let's set up the logic that will call the tracking endpoint:</p><pre><code class="line-numbers language-javascript"><script type="text/plain">
const trackingApiOptions = {
    host: config.sitecoreApiHost,
    querystringParams: {
        sc_apikey: config.sitecoreApiKey, 
    }, 
    fetcher: dataFetcher
}; 

const track = (event: string) => {
    return function (dispatch: any, getState: any) {
        trackingApi
            // note the events are an array - batching is supported
            .trackEvent([{ eventId: event }], trackingApiOptions)
            .then(() => {
                dispatch(artistSearchedClickedRequest);
            })
            .catch((error: any) => console.error(error));
    }
}
</script></code></pre><p>The <em>Tracking API Options</em> object will provide the host, the Sitecore API key (by query string) and a data fetcher. This data fetcher will be a simple implementation of Axios but feel free to use whatever implementation you want:</p><pre><code class="line-numbers language-javascript"><script type="text/plain">
import axios from "axios";

export function dataFetcher(url, data) {
  return axios({
    url,
    method: data ? 'POST' : 'GET',
    data,
    withCredentials: true,
  });
}
</script></code></pre><p>Make sure that when you start triggering one of the marketing events you must stop the user session. Only then the data will be committed and you'll see data appear in the <em>Experience Analytics</em>:</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2019/02/graphics.png" class="kg-image"></figure>
