---
layout: post
title: 'Sitecore JSS: Create your own GraphQL schema'
description: >-
  Learn how to configure Sitecore and create your own GraphQL schema and use external API&#x27;s combined with Sitecore&#x27;s GraphQL schema&#x27;s
date: 2018-09-06 08:21:12.000000000 +01:00
image: >-
  https://images.unsplash.com/photo-1502780787199-5bdc5502db3f?ixlib&#x3D;rb-0.3.5&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ&amp;s&#x3D;37ef0d8313f3e6b2d1525918918a1462
optimized_image: >-
  https://images.unsplash.com/photo-1502780787199-5bdc5502db3f?ixlib&#x3D;rb-0.3.5&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ&amp;s&#x3D;37ef0d8313f3e6b2d1525918918a1462
category: blog
tags:
  - jss
  - sitecore
  - graphql
author: garywenneker
paginate: true
---
<p>One very great feature is added to the Sitecore JSS module: <a href="https://graphql.org" target="_blank">GraphQL</a> support. It allows you to query an API with a JSON payload. Compared to REST API's GraphQL is more maintainable and more flexible as GraphQL services can be stiched together.</p>
<blockquote>
<p>GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data.</p>
</blockquote>
<h2 id="buildingthesitecoregraphqlschemaprovider">Building the Sitecore GraphQL SchemaProvider</h2>
<p>In this post I will walk through the following steps to guide you setting up a custom GraphQL SchemaProvider from which you can query the enpoint.</p>
<h3 id="stepstocreateyourowngraphqlschemainsitecore">Steps to create your own GraphQL schema in Sitecore:</h3>
<ul>
<li>Getting my initial information at Sitecore's JSS website</li>
<li>Setting up configuration in Sitecore</li>
<li>Create project with SchemaProvider, GraphType(s) and RootQuery</li>
<li>Adding Arguments</li>
<li>Using different FieldTypes</li>
<li>Query the custom API</li>
</ul>
<p>The first step is to look at the example on the <a href="https://jss.sitecore.net/docs/techniques/graphql/graphql-overview">JSS website</a> under the <em>Techniques</em> section. I have based my code on this example to keep it as simple as possible. When I wrote this post GraphQL was installed via installing JSS, but it should be available separately at launch (probably Oct. 2018).</p>
<p>First you need to set <code>&lt;compilation debug=&quot;true&quot;&gt;</code> in web.config enabling the GraphQL UI. If you already have been playing around with JSS you also have an <a href="https://doc.sitecore.net/sitecore_experience_platform/developing/developing_with_sitecore/sitecoreservicesclient/api_keys_for_the_odata_item_service">SSC API key</a> in place in de Core Database. If not, make sure to do so.</p>
<h2 id="settingupthegraphqlconfigurationinsitecore">Setting up the GraphQL configuration in Sitecore</h2>
<p>On the JSS website the configuration is just an example (<strong>but not a working one</strong>). Below is a working example of the schema we are building. We need to do two things to make t work:</p>
<ul>
<li>
<p><strong>Define configuration for the schema provider</strong> - Under <code>sitecore/api/GraphQL/defaults/content/schemaProviders</code> add an XML element like <code>&lt;Movies type=&quot;Macaw.GraphQL.Examples.MovieSchemaProvider, Macaw.GraphQL.Examples&quot;/&gt;</code>. The element name is of your choice and can be anything. This will be used in the next step.</p>
</li>
<li>
<p><strong>Define configuration for the endpoint</strong> - Registering your SchemaProvider is not enough. You need to add configuration for your endpoint. In my example below (which you can use as a basis) you'll see that the endpoint is created under <code>sitecore/api/GraphQL/endpoints</code>. Also here you can create an XML node with a name of your choise. you have to define the schema you're using. This can be accomplished by adding <code>&lt;schema hint=&quot;list:AddSchemaProvider&quot;&gt;&lt;content ref=&quot;/sitecore/api/GraphQL/defaults/content/schemaProviders/Movies&quot; param1=&quot;master&quot; /&gt;&lt;/schema&gt;</code>.</p>
</li>
</ul>
<pre class="line-numbers"><code class="language-markup">
<script type="text/plain">
<configuration xmlns:patch="http://www.sitecore.net/xmlconfig/" 
    xmlns:set="http://www.sitecore.net/xmlconfig/set/" 
    xmlns:role="http://www.sitecore.net/xmlconfig/role/">
    <sitecore>
        <api>
            <GraphQL>
            
                <defaults>
                    <content>
                        <schemaProviders>
                            <Movies type="Macaw.GraphQL.Examples.MovieSchemaProvider, Macaw.GraphQL.Examples"/>
                        </schemaProviders>
                    </content>
                </defaults>

                <endpoints>
                    <movies url="/sitecore/api/graph/items/movies" type="Sitecore.Services.GraphQL.Hosting.GraphQLEndpoint, Sitecore.Services.GraphQL.NetFxHost" resolve="true">
                        <url>$(url)</url>

                        <enabled role:require="!ContentDelivery">true</enabled>
                        <enabled role:require="ContentDelivery">false</enabled>

                        <gui role:require="!ContentDelivery">DebugOnly (follow web.config compilation setting)</gui>
                        <gui role:require="ContentDelivery">false</gui>

                        <schema hint="list:AddSchemaProvider">
                            <content ref="/sitecore/api/GraphQL/defaults/content/schemaProviders/Movies" param1="master" />
                        </schema>
                        <security ref="/sitecore/api/GraphQL/defaults/security/publicService" />
                        <performance ref="/sitecore/api/GraphQL/defaults/performance/standard" />
                        <cache type="Sitecore.Services.GraphQL.Hosting.QueryTransformation.Caching.GraphQLQueryCache, Sitecore.Services.GraphQL.NetFxHost">
                            <param desc="name">$(url)</param>
                            <param desc="maxSize">10MB</param>
                        </cache>
                        <extenders hint="list:AddExtender">
                        </extenders>
                    </movies>

                </endpoints>
            </GraphQL>
        </api>
    </sitecore>
</configuration>

</script>
<p></code></pre></p>
<h2 id="creatingaschemaprovider">Creating a SchemaProvider</h2>
<p>For this example I created a class library. When I started developing the SchemaProvider I wanted to expose data from <a href="https://www.themoviedb.org/">The Movie Database</a> For this I use the <a href="https://github.com/LordMike/TMDbLib">TMDbLib Nuget package</a>. After building my library I copied the dll to the bin folder of my website's webroot.</p>
<h3 id="sourcefortheschemaproviderclass">Source for the SchemaProvider class:</h3>
<pre class="line-numbers"><code class="language-csharp">
using System;
using System.Collections.Generic;
using System.Web;
using GraphQL.Resolvers;
using GraphQL.Types;
using Sitecore;
using Sitecore.Security;
using Sitecore.Security.Accounts;
using Sitecore.Services.GraphQL.Schemas;
using TMDbLib.Client;
using TMDbLib.Objects.Movies;

namespace Macaw.GraphQL.Examples
{
    class MovieSchemaProvider : SchemaProviderBase
    {
        public override IEnumerable<FieldType> CreateRootQueries()
        {
            yield return new MovieQuery();
        }
        protected class MovieQuery : RootFieldType<MovieGraphType, Movie>
        {
            public MovieQuery() : base(name: "movieQuery", description: "Gets some movie data")
            {
                QueryArgument[] queryArgumentArray = new QueryArgument[1];
                int index1 = 0;
                QueryArgument<StringGraphType> queryArgument1 = new QueryArgument<StringGraphType>();
                queryArgument1.Name = "movieId";
                queryArgument1.Description = "The movie ID to get";
                queryArgumentArray[index1] = (QueryArgument)queryArgument1;

                this.Arguments = new QueryArguments(queryArgumentArray);
            }
            protected override Movie Resolve(ResolveFieldContext context)
            {
                int movieId = context.GetArgument<int>("movieId", (int)383498);

                TMDbClient client = new TMDbClient("APIKEY");
                Movie movie = client.GetMovieAsync(movieId, MovieMethods.Credits | MovieMethods.Similar).Result;
                return movie;
            }
        }
        protected class MovieGraphType : ObjectGraphType<Movie>
        {
            public MovieGraphType()
            {
                Name = "TMDbLibMovie";

                Field<NonNullGraphType<StringGraphType>>("id", resolve: context => context.Source.Id);
                Field<NonNullGraphType<StringGraphType>>("title", resolve: context => context.Source.Title);
                Field< ListGraphType< KeywordGraphType >>("keywords", resolve: context => context.Source.Keywords.Keywords);
                Field<NonNullGraphType<StringGraphType>>("posterpath", resolve: context => context.Source.PosterPath);
                Field<NonNullGraphType<StringGraphType>>("overview", resolve: context => context.Source.Overview);
                Field<NonNullGraphType<StringGraphType>>("backdropPath", resolve: context => context.Source.BackdropPath);
                Field<NonNullGraphType<StringGraphType>>("budget", resolve: context => context.Source.Budget);
                Field< ListGraphType < GenreGraphType >> ("genres", resolve: context => context.Source.Genres);
                Field<NonNullGraphType<StringGraphType>>("releaseDate", resolve: context => context.Source.ReleaseDate);
                Field<NonNullGraphType<StringGraphType>>("voteAverage", resolve: context => context.Source.VoteAverage);
                Field<NonNullGraphType<StringGraphType>>("voteCount", resolve: context => context.Source.VoteCount);
            }
        }
        protected class GenreGraphType : ObjectGraphType<TMDbLib.Objects.General.Genre>
        {
            public GenreGraphType()
            {
                Name = "TMDbLibMGenre";
                Field<NonNullGraphType<StringGraphType>>("id", resolve: context => context.Source.Id);
                Field<NonNullGraphType<StringGraphType>>("title", resolve: context => context.Source.Name);
            }
        }

        protected class KeywordGraphType : ObjectGraphType<TMDbLib.Objects.General.Keyword>
        {
            public KeywordGraphType()
            {
                Name = "TMDbLibMKeyword";
                Field<NonNullGraphType<StringGraphType>>("id", resolve: context => context.Source.Id);
                Field<NonNullGraphType<StringGraphType>>("title", resolve: context => context.Source.Name);
            }
        }
    }
}

</code></pre>
<blockquote>
<p>NOTE: this example is using nested classes because it's a very very small schema. A real schema of any size should split the RootFieldTypes and GraphTypes into separate files.</p>
</blockquote>
<h4 id="addingarguments">Adding arguments</h4>
<p>By default our Query class won't accept any attributes like the <code>movieId</code> in our example. We accomplish this by adding <code>QueryArguments</code> in the contructor of the Query. These are used in the <code>Resolve</code> method where we can get the arguments like in our example: <code>int movieId = context.GetArgument&lt;int&gt;(&quot;movieId&quot;, (int)383498);</code></p>
<h4 id="graphqlfieldtypes">GraphQL FieldTypes</h4>
<p>By default we will retrieve the field values as a string. In many cases we need to use other types like booleans, floats, lookups etc. For our demo I constructed two custom GraphTypes and constructed a ListGraphType from this. The result is that I now can also query the genres of the movie rather than getting an exception when querying the field.</p>
<h3 id="queringthegraphqlapi">Quering the GraphQL API</h3>
<p>Now we have deployed our schema and configuration we now can test it. In the configuration I added the url <code>/sitecore/api/graph/items/movies</code>. Hiting the url will now result in the following error: <code>{&quot;errors&quot;:[{&quot;message&quot;:&quot;SSC API key is required. Pass with 'sc_apikey' query string or HTTP header.&quot;}]}</code>. This is because my example forces you to provide the SCC API key. This can be done by adding <code>?sc_apikey={YOUR_API_KEY}</code> in the querystring. Adding <code>/ui</code> will show the Sitecore Experience Graph Browser.</p>
<p><img src="/assets/img/uploads/2018/09/2018-09-07-11_35_43-Desktop.png" alt=""></p>
<p>From here you can test your queries. In the given example I added a movie query:</p>
<pre><code class="line-numbers language-javascript">
{
  movieQuery(movieId: "353081") {
    id,
    title,
    posterpath,
    overview,
    budget,
    genres {
      id,
      title
    }
    releaseDate,
    voteAverage,
    voteCount
  }
}	
</code></pre>
<p>After firing the query we now get results:</p>
<p><img src="/assets/img/uploads/2018/09/2018-09-07-11_47_39-Desktop.png" alt=""></p>
<p>The query is created with ease. Intelisense will help us construct the query. On the right side we can inspect the schema and find all available fields we can access. Happy coding :)</p>
