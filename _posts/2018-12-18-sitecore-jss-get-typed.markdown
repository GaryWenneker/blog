---
layout: post
title: 'Sitecore JSS: Get Typed!'
description: >-
  A post about setting up and integrating static type checking in your Sitecore Javascript Services project. The examples given are Flow and TypeScript
date: 2018-12-18 08:21:12.000000000 +01:00
image: >-
  https://images.unsplash.com/photo-1485796826113-174aa68fd81b?ixlib&#x3D;rb-1.2.1&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ
optimized_image: >-
  https://images.unsplash.com/photo-1485796826113-174aa68fd81b?ixlib&#x3D;rb-1.2.1&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ
category: blog
tags:
  - sitecore
  - react
author: garywenneker
paginate: true
---
---
<p>In this post I will get you setting up typing in place in your JSS application. You might ask what it is for and why you would use it? Because it is great! It gives you type checking, forcing you to use types/interfaces and best of all, you'll have intellisense!</p><p>Because the Sitecore Javascript Services template is in plain Javascript we have to add some extra's to make the typings work. We can go with two directions: <a href="https://flow.org/en/">Flow</a> or TypeScript. Because the way JSS builds it's bundles we must keep the actual js-files for the components.</p><p>What I want to accomplish is that I change from the default script...</p><pre><code class="line-numbers language-javascript"><script type="text/plain">
import React from 'react';
import { Text, RichText } from '@sitecore-jss/sitecore-jss-react';

/**
 * A simple Content Block component, with a heading and rich text block.
 * This is the most basic building block of a content site, and the most basic
 * JSS component that's useful.
 */
const ContentBlock = ({ fields }) => (
  <React.Fragment>
    <Text tag="h2" className="display-4" field={fields.heading} />

    <RichText className="contentDescription" field={fields.content} />
  </React.Fragment>
);

export default ContentBlock;
</script></code></pre><p>...to a typed component</p><pre><code class="line-numbers language-javascript"><script type="text/plain">
import React from 'react';
import { Text, RichText } from '@sitecore-jss/sitecore-jss-react';
import { Interface } from 'readline';

interface IFields {
  heading: string,
  content: string,
}

interface Props { 
  fields: IFields
 };

interface State {

};

class ContentBlock extends React.Component<Props, State> {

  constructor(props: any) {
    super(props);
  }
  render() {
    const { fields } = this.props
    return (
      <React.Fragment>Awsome shit
        <Text tag="h2" className="display-4" field={fields.heading} />
        <RichText className="contentDescription" field={fields.content} />
      </React.Fragment>
    );
  }
}

export default ContentBlock;

</script></code></pre><p></p><h3 id="flow">Flow</h3><blockquote>Flow is a static typechecker for javascript</blockquote><p>Using flow you can keep on working in the JS, you just need to do some extra's:</p><ol><li>Install Flow by using the command <strong><code>npm install -g flow-bin</code></strong></li><li>From the root of your project initialize Flow using the command <code><strong>flow init</strong></code>. Run this command at the top level of your project to create one, empty file called <a href="https://flow.org/en/docs/config/"><code>.flowconfig</code></a>. At its most basic level, <code>.flowconfig</code> tells the Flow background process the root of where to begin checking Flow code for errors.</li><li>Add <code><strong>// @flow</strong></code> to the top of your component. You now have the full power of Flow and its type-checking available to you.</li></ol><p>When we now try to access properties on our component suddenly also have the intellisense:</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/12/2018-12-16-09_12_13---index.js---jssflow---Visual-Studio-Code--Administrator--1.png" class="kg-image"></figure><p>Working with Flow can give you a bit of a headache. I use Visual Studio Code a lot. One of the built in extensions is the TypeScript and JavaScript Language Features. This one will start to interfere with Flow. What you need to do is set <code>javascript.validate.enable</code> option to <code>false</code><strong>or</strong> completely disable the built-in TypeScript extension for your project. </p><h3 id="typescript">TypeScript</h3><blockquote>TypeScript is a superset of JavaScript which primarily provides optional static typing, classes and interfaces. One of the big benefits is to enable IDEs to provide a richer environment for spotting common errors as you type the code. To get an idea of what I mean, watch Microsoft's introductory video on the language.</blockquote><p>That's a mouth full! Flow is created and used by Facebook and when you search you'll see that more and more companies move to TypeScript. As an independent developer you could of course use your own poison but when working in teams the choice is often already made. Where I work we also use TypeScript, which is fine because I have been using it for some years now.</p><p>Main goal with this approach is that we still need to have a js-file as output. The scaffolding of a component outputs this for us and building our solution will require this too. If not you will get some unexpected behavior that your component is not there:</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/12/cb.png" class="kg-image"></figure><p>To get TypeScript working in JSS we need to do the following things:</p><ol><li>Make sure you have TypeScript installed: <code><strong>npm i -g typescript</strong></code></li><li>Add the following packages to your project: <code><strong>npm install --save typescript @types/node @types/react @types/react-dom @types/jest</strong></code></li><li>Add <code><strong>"start:tsc": "tsc -p .",</strong></code> to your <code>application.json</code> file</li><li>Change the start script in <code>application.json</code> to <code><strong>"start": "npm-run-all --serial bootstrap:disconnected --parallel start:react start:proxy start:watch-components start:tsc",</strong></code></li></ol><p>As an example rename the index.js of the ComtentBlock component by giving it a <code>.tsx</code> extension. Also replace the contents of that component with the types code from this post (second piece of code). Make sure that line one is changed to <code><strong>import * as React from 'react';</strong></code> When you now run the command <code><strong>tsc ./src/components/ContentBlock/index.tsx</strong></code> it will give you the following errors:</p><figure class="kg-image-card kg-width-full"><img src="/content/images/2018/12/whoops.png" class="kg-image"><figcaption>The Sitecore JSS types have no default exports and therefore will throw an exception</figcaption></figure><p>By default, the TypeScript compiler will process all files from the root. This includes the <code>node_modules</code> folder. Therefore we must add some extra configuration for the compiler. Create a file with the name <code><strong>tsconfig.json</strong></code> and paste the JSON from below. The error messages will be removed by setting <code>allowSyntheticDefaultImports</code> to <code><strong>true</strong></code> . For the rest, just play around with the settings and see what suits your needs.</p><pre><code class="line-numbers language-javascript">
{
    "compileOnSave": true,
    "compilerOptions": {
        "strictNullChecks": false, // enable strict null checks as a best practice
        "lib": ["dom", "es2015"],
        "module": "es2015", // specify module code generation
        "jsx": "react", // use typescript to transpile jsx to js
        "target": "es5", // specify ECMAScript target version
        "moduleResolution": "node",
        "alwaysStrict": false,
        "strictFunctionTypes": false, // disables bivariant parameter checking for function types
        "strictPropertyInitialization": false, // ensures non-undefined class properties
        "noImplicitAny": false, // warn on expresions and declarations with an implied 'any' type
        "noEmitOnError": true, // do not emit  outputs if any type checking errors were reported
        "removeComments": false, // do not commit comments to output
        "sourceMap": false, // generates corresponding .map files
        "watch": true, // watch input files
        "allowJs": false, // allow javascript files to be compiled
        "allowSyntheticDefaultImports": true, // allow default inports from modules with no default exports
    },
    "exclude": [
        "node_modules/**/*",
    ],
    "include": [
        "./src/**/*.tsx"
    ],
    "typeRoots": [
        "node_modules/@types",
        "../@types"
    ]
}
</code></pre><p></p><h3 id="takeaways">Takeaways</h3><p>Developing in just plain Javascript can be pretty tedious without type checking. Adding type checking to your project will make it more maintainable and will make the code easier to read. The compiler shows warnings about potential errors and you'll get intellisense. Also developing with TypeScript feels more like you are developing in C# :-)</p>
