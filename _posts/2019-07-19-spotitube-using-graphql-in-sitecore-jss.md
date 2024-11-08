---
layout: post
title: 'SpotiTube: Using GraphQL in Sitecore JSS'
description: >-
  Learn how to build a Sitecore JSS  application that uses GraphQL services.
date: 2019-07-19 08:21:12.000000000 +01:00
image: >-
  /assets/img/uploads/2019-07-19-09_13_31-.png
optimized_image: >-
  /assets/img/uploads/2019-07-19-09_13_31-.png
category: blog
tags:
  - jss
  - sitecore
  - graphql
  - nodejs
  - reactjs
  - nvm
  - git
  - css
  - headless
  - cms
  - typescript
author: garywenneker
paginate: true
---
> Talk data to me!

![](/assets/img/uploads/2019/07/image-11.png)

Working with JSS is a growing thing and more and more sites are built with Sitecore's headless technology. It allows you to build application based websites with an enormous speed something unknown in the world of "classic" ASP.NET MVC development. This post is about a sample application I build to show how GraphQL can be used in Sitecore Javascript Services.

For this demo I created a Spotify Web front end and attached a GraphQL backend to it. This app will not use any Sitecore items but it rather consumes the GraphQL service which fetches data from the Spotify API. And this is the soul purpous of this post.

Back to the amount of time creating the app and backend services: 20 hours!

<figure class="kg-embed-card"><iframe width="480" height="270" src="https://www.youtube.com/embed/Ms-z2iRbTXM?feature=oembed" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen=""></iframe>

<figcaption>SpotiTube in action</figcaption>

</figure>

### Why GraphQL and not REST

Let's say we want to make a news site. The front page will be an overview page and the pages after that one will be detail pages. This means the overview page shows a list of items where the detail page will show the context of a route. Let's focus on the detail page. The data for this page can be retrieved by parsing the data from the JSS route call. But what if we want related articles to show up on the detail page? Then we need an extra call for this list. We can do this in several ways, extend the layout service, do some magic in code or use services. We're going with option two. What flavour? The existing REST services? Or GraphQL? Did you know that all data from Sitecore can be queried using the [Sitecore JSS GraphQL endpoint](https://jss.sitecore.com/docs/techniques/graphql/graphql-overview)?

![](/assets/img/uploads/2019/07/image-5.png)

<figcaption>Fake news!!!</figcaption>



If you start searching on Google using keywords like REST and GraphQL you find many statements that GraphQL is the new REST. This is actually not true and people are comparing apples to oranges. I am not going to give you the explanation bause the only good on has been given by Zdenek "Z" Nemec who did a [keynote](https://goodapi.co/blog/rest-vs-graphql) on this topic at Nordic APIs Platform Summit 2018\. Also check out his video, it's brilliant!

<figure class="kg-embed-card"><iframe width="480" height="270" src="https://www.youtube.com/embed/yLf0rIaRtRc?feature=oembed" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen=""></iframe>

<figcaption>REST vs. GraphQL</figcaption>

</figure>

So back to our choice, with REST we have to make separate calls and do some magic regarding child-parent constraints of our data where GraphQL does that for us as shown in the model below.

![](/assets/img/uploads/2019/07/image-2.png)

<figcaption>REST output vs GraphQL output</figcaption>



### Implementing the GraphQL client in JSS

Now that we know that with GraphQL we have one endpoint which we can use as a query mechanism we now should look in how the service has to be created. For the demo I used the mechanism of a GraphQL services which does not run in context of Sitecore. But if you want to know how to do that within the Sitecore context then take a look at the [repo](https://github.com/Sitecore-Hackathon/2019-Cheese-n-Frog) of the Sitecore Hackathon 2019 project we did.

![](/assets/img/uploads/2019/07/image-6.png)

Using GraphQL.NET it's very easy to implement your own schema. First Create a Repository that exposes your models. Then create GraphTypes. They will handle property mapping for your service. In the startup of your service all is registered so it will be available through dependency injection.

The controller exposes the GraphQL schema and this exposes the Query and Mutation logic. Hitting _F5_ from Visual Studio will start the service and will popup [GraphiQL](https://github.com/graphql/graphiql), the tool where you can create and test your queries.

<figure class="kg-embed-card"><iframe width="480" height="270" src="https://www.youtube.com/embed/1nPkS7bWF-Y?feature=oembed" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen=""></iframe></figure>

### Creating components

Now it's time to create the components. And we are going to do that the normal JSS way using `jss scaffold **<componentName>**`and give them the markup and styling.

![](/assets/img/uploads/2019/07/image-9.png)

<figcaption>Componetizing </figcaption>


### Global state management with Redux & Placeholders

Now we can start doing the fun stuff: connecting our components to the GraphQL API and let them communicate with eachother. We accomplish this by using [React Redux](https://redux.js.org/). This allows the components to subscribe to changes in the global state, which are triggered by actions. So follow the [link to the Redux website](https://redux.js.org/) if you want to know how Redux works.

![](/assets/img/uploads/2019/07/image-12.png)

First we create a Query:

{% highlight javascript %}
    //"3RGLhK1IP9jnYFH4BRFJBS"
    export const GET_ARTIST_QUERY = gql`
        query artist($id: String!){
        artist(id: $id) {
                id
                name
                followers
                popularity
                images {
                height
                url
                width
                }
                relatedArtists {
                    id
                    name
                    images {
                        url
                    }
                }
                albums(limit: 50) {
                    id
                    name
                    images {
                        height
                        width
                        url
                    }
                }
            }
        }
    `;
{% endhighlight %}

Then use this in the action:

{% highlight javascript %}
    const getArtistData = (id: string) => {
        return function (dispatch, getState) {
            var variables = {id: id};
            return client.query({ query: GET_ARTIST_QUERY, variables: variables }).then(function (response) {
                dispatch(clearAlbum())
                dispatch(clearTracks())
                dispatch(success(response))
            }).catch(function (error) {
                //console.log(error)
            })
        }
    }

    const success = (ArtistData: any) => {
        return {
            type: TypeKeys.GET_Artist,
            payload: ArtistData,
            ArtistData
        }
    }
{% endhighlight %}

Commit the changes:

{% highlight javascript %}
    export const ArtistReducer = (state = initState, action: ActionTypes) => {

        switch (action.type) {

            case TypeKeys.GET_Artist:
                return {...state, ...action.payload.data.artist};

            default:
                return state;
        }
    }
{% endhighlight %}

And now we can connect the components to the global store:

{% highlight javascript %}
    // global state mapped to the component's properties
    const mapStateToProps = (state) => {
      return {
        artist: {}
      }
    }

    // global actions mapped to the component's properties
    const mapDispatchToProps = (dispatch) => {
      return {
        getArtist: (id: string) => dispatch(getArtistData(id)),
        getsearchSingleArtist: (query: string) => dispatch(getsearchSingleArtist(query))
      }
    }

    // connect them all
    export default connect(mapStateToProps, mapDispatchToProps)(UIHeader);
{% endhighlight %}

_For the actual sources check the GitHub Repos._

Finally we want our components to have placeholders. This is not necessary fore my demo but I think it hives a nice touch using custom components and having the ability to use them or exchange them in the Experience Editor.

{% highlight javascript %}
    const mapStateToProps = (state) => {
      return {
        artist: state.artist,
        tracks: state.tracks,
        album: state.album,
        currentTrack: state.tracks.currentTrack,
      }
    }
    const mapDispatchToProps = (dispatch) => {
      return {

      }
    }

    const withPlaceholderInjected = withPlaceholder([
      {
        placeholder: 'jss-hero',
        prop: 'hero',
      },
      {
        placeholder: 'jss-albums',
        prop: 'albums',
      },
      {
        placeholder: 'jss-related-artists',
        prop: 'relatedArtists',
      },
      {
        placeholder: 'jss-tracklist',
        prop: 'tracklist',
      },
      {
        placeholder: 'jss-playlist',
        prop: 'playlist',
      }
    ])(UIContent);

    const withPlaceholderAndSitecoreContext = withSitecoreContext()(
      withPlaceholderInjected
    );

    export default connect(mapStateToProps, mapDispatchToProps)(withPlaceholderAndSitecoreContext);
{% endhighlight %}

The withPlaceholderInjected function creates properties on your component which holds the placeholders. These can now be added like `{ this.props._PropName_ }`.

### Resources

*   The [Spotify GraphQL API](https://github.com/GaryWenneker/spotify-graphql-api)
*   The Sitecore JSS [SpotiTube App](https://github.com/GaryWenneker/spotitube)

_**Note**: In `appsettings.json` in the API on line 13 add your own [Spotify API key](https://developer.spotify.com/documentation/web-api/quick-start/) (get youw own at [https://developer.spotify.com](https://developer.spotify.com/))_

> **Happy playing!!!**

<figure class="kg-embed-card"><iframe width="480" height="270" src="https://www.youtube.com/embed/wiLIJcHyNkE?feature=oembed" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen=""></iframe></figure>