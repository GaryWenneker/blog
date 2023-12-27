---
layout: post
title: 'Sitecore JSS: Triggering Marketing Events'
description: >-
  Trigger marketing events from Sitecore JSS and track visitors during current and past web sessions for personalization, reporting and optimizing your website.
date: 2019-02-03 08:21:12.000000000 +01:00
image: >-
  /assets/img/uploads/marketing.png
optimized_image: >-
  /assets/img/uploads/marketing.png
category: blog
tags:
  - jss
  - sitecore
  - nodejs
  - reactjs
  - headless
  - cms
  - typescript
  - mvc
  - design
  - marketing
author: garywenneker
paginate: true
---
With Sitecore JSS the development strategy has shifted. It enables frontend developers to participate in Sitecore development teams, whether you are creating a single page application (SPA) or consuming the JSS layout service providing you with the context of the route. User interaction is done on the client side which offers us certain advantages and challenges. One of the advantages we get is that we can trigger marketing events from the frontend with just a few lines of code.

The following steps will show you how to set up your Sitecore and frontend environment and how to trigger an event or marketing goal from your frontend code.

* * *

### Deploying the marketing definitions

Before we're able to trigger an event we have to deploy the marketing definitions. This is done from the control panel _(Dashboard -> Control Panel)_. Click on the **_Deploy marketing definitions_** link. This will open a window with all the marketing definitions. Select them all and click **_Deploy_**. This might take about 15 minutes to run so don't think your system renders unresponsive and please don't be impatient :-)

![](/assets/img/uploads/2019/02/2019-02-03-08_56_36-Control-Panel.png)

### Setting up custom goals and events

From JSS we can track several items:

*   **Goal** - Goals are activities that visitors can perform on your website. You create goals to track and measure how visitors engage with the website and campaigns – both online and offline.
*   **Outcome** - An outcome is the business significant result of a dialog between a contact and a brand. Through a contact's path to becoming a customer, you can track the events they have triggered and the goals they have converted on their journey. This provides you with an insight into how contacts interact with your website, as well as the relative financial value they have for your organization.
*   **Event** - Events track visitor activity on a website. Tracking events helps build up a more complete picture of a visitor’s behavior as they navigate your website. You should assign engagement value points to all events to reflect their relative importance to your organization.
*   **PageView** - Each time a page is viewed, an event is triggered.
*   **Campaign** - The campaign which can be triggered.

Now we have set up the marketing definitions we can start creating a marketing event. On the dashboard under _Marketing Applications_ click on _Marketing Control Panel_. This will bring up a content editor interface. Right-click the _Goals_ node, click on _Insert_ and select _Goal_. Give it a name you want.

![](/assets/img/uploads/2019/02/goal.png)

In the content window expand the _Data_ section and add a value to the _points_ field. Very important for you to know that this goal is not yet deployed! To do so, click on the _Review_ tab and hit the _Deploy_ button. Now we can dive into the code on the client side.

### Sitecore JSS Tracking API

The Tracking API can track several things by making a request to the Sitecore Layout Service. It accepts an array of specific types (with their properties):

*   **Campaign**
    *   _campaignId_: `string`
*   **Goal**
    *   _goalId_: `string`
*   **Event**
    *   _eventId_: `string`
*   **Outcome**
    *   _outcomeId_: string
    *   _currencyCode_: `[nullable]` `string`
    *   _monetaryValue_: `[nullable]` `number`
*   **PageView**
    *   _pageId_: `string`
    *   _url_: `string`

When we observe the definition for the Tracking API we'll see that it accepts an array:

{% highlight javascript %}
    export declare function trackEvent(events: Array<EventInstance | GoalInstance | OutcomeInstance | CampaignInstance | PageViewInstance>, options: TrackingRequestOptions): Promise<void>;
{% endhighlight %}

Let's set up the logic that will call the tracking endpoint:

{% highlight javascript %}
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
{% endhighlight %}

The _Tracking API Options_ object will provide the host, the Sitecore API key (by query string) and a data fetcher. This data fetcher will be a simple implementation of Axios but feel free to use whatever implementation you want:

{% highlight javascript %}
    import axios from "axios";

    export function dataFetcher(url, data) {
      return axios({
        url,
        method: data ? 'POST' : 'GET',
        data,
        withCredentials: true,
      });
    }
{% endhighlight %}

Make sure that when you start triggering one of the marketing events you must stop the user session. Only then the data will be committed and you'll see data appear in the _Experience Analytics_:

![](/assets/img/uploads/2019/02/graphics.png)