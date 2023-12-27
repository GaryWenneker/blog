---
layout: post
title: 'Sitecore JSS: React Hooks Global State'
description: >-
  When developing in Sitecore JSS I am always happy to do that in React. Most of the React components I&#x27;ve been building are small ones, rendering the context of that data. That&#x27;s not special at all and for this I use the React Function Component. It has no side effects.
date: 2020-02-14 08:21:12.000000000 +01:00
image: >-
  https://images.unsplash.com/photo-1566937169390-7be4c63b8a0e?ixlib&#x3D;rb-1.2.1&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ
optimized_image: >-
  https://images.unsplash.com/photo-1566937169390-7be4c63b8a0e?ixlib&#x3D;rb-1.2.1&amp;q&#x3D;80&amp;fm&#x3D;jpg&amp;crop&#x3D;entropy&amp;cs&#x3D;tinysrgb&amp;w&#x3D;1080&amp;fit&#x3D;max&amp;ixid&#x3D;eyJhcHBfaWQiOjExNzczfQ
category: blog
tags:
  - sitecore
  - react
  - state
author: garywenneker
paginate: true
---
When developing in Sitecore JSS I am always happy to do that in React. Most of the React components I've been building are small ones, rendering the context of that data. That's not special at all and for this I use the React [Function Component](https://reactjs.org/docs/components-and-props.html#function-and-class-components). It has no side effects. Where [Class Components](https://reactjs.org/docs/components-and-props.html#function-and-class-components) has [state](https://reactjs.org/docs/state-and-lifecycle.html) and livecycle methods the Function Component has not. This means the Function Component only renders the markup.

> Previously, refs were only supported for Class-based components. With the advent of React Hooks, that's no longer the case

## Common issues with React

### Wrapper Hell

Wrapper Hell is a situation where many HOC's ([Higher Ordered Components](https://reactjs.org/docs/higher-order-components.html)) are used. HOC's wrap your component to add extra logic, e.g. connecting an API or a GraphQL module to interact with the GQL's interface of Sitecore. When the output is rendered to the browser this is what happens:

![Wrapper Hell](/assets/img/uploads/2020/02/wrapper_hell_2.png)

### Minification issues

Compilers cannot handle the HOC code very well, functions in bundles will have the same name and the bundles are a lot larger than when you would use a function component. Bundles with only Function Components are compared to bundles with Class Components very small.

## React Hooks

But of course I am not only building components that only render. At my current client (Health Insurance) they also need behavior in those components, components which function as small applications sitting there in the page. This could be a small form where you can fill in your email address or even a complete funnel.

For this to work we need Side Effects:

> "Side Effect" is not a react-specific term. It is a general concept about behaviours of functions. A function is said to have side effect if it trys to modify anything outside its body. For example, if it modidifies a global variable, then it is a side effect.

These side effects can be used in React Function Components using Hooks. Hooks are a new addition in React 16.8\. They let you use state and other React features without writing a class. This eliminates the [Wrapper Hell](https://reactjs.org/docs/hooks-intro.html#its-hard-to-reuse-stateful-logic-between-components) and better optimization and smaller bundles.

## State and Component Behavior

Yes! Now we can use state and functions in our Function Components. I am not going to go in depth about how it because the [examples](https://reactjs.org/docs/hooks-intro.html) on the React website are easy to follow. In most cases you will use the `useState`, `useEffect` and `useRef` hooks. You can even write your own!

## React Hooks Global State

And that's what I did. `useState` manages state within your component and does not share it with other components. I could use a HOC (and yes I created such an example) but this would lead back to the wrapper hell all over again. I also can use the [useContext](https://reactjs.org/docs/hooks-reference.html#usecontext) hook but that feels kinda hacky and you **must** wrap all your components in a Provider and all comsuming components in a Consumer component.

### React Hookz

[React Hookz](https://github.com/GaryWenneker/react-hookz/blob/master/README.md) is a project I started to make it possible to share state and actions between components.

> React Global Hookz, a simple global state for React with the Hooks API in less than 1kb written in TypeScript

React Hookz can be set up with just a few lines of code:

```js
    const useReactHookz `= ReactHookz(React, initialState, actions);
    export default useReactHookz;
```

the `initialState` can be any object and the `actions` is a class with functions changing that state. React is the imported React namespace and passed through to React Hookz.

### Usage

[React Hookz](https://github.com/GaryWenneker/react-hookz/blob/master/README.md) can be used in a Function Component with just a few lines of code:

### Actions example

```js
    export interface IActions {
      addToCounter: (amount: number) => void;
    }

    export interface IState {
      counter: number;
    }

    export interface IStore {
      state: IState;
      setState: (state: any) => void;
    }

    export const addToCounter = (store: IStore, amount: number, callback: any) => {
      const counter = store.state.counter + amount;
      store.setState({ counter });
      if (callback) {
        callback();
      }
    };

    const actions = { addToCounter };

    const initialState: IState = {
      counter: 1
    };

    export default useReactHookz(React, initialState, actions);
```

If you have been working a lot with React you may notice that setting a state with React Hooks compared to the old way does not accept a callback function. Setting state with React Hooks is asynchronous which means it can be hard when using a glbal context.

React Hookz on the other hand allows you to provide a callback function!

### Component example

```js
    import React from "react";
    import ReactHookz from "react-hookz";
    import { renderTime } from "../utils/index";
    import useReactHookz, { IState, IActions } from "../actions";

    const addedCounter = () => {
        console.log('callback: added the counter');
    };

    const Counter: React.FC = () => {
      // using ReactHookz
      const [state, actions]: [IState, IActions] = useReactHookz();
      return (
        <div className="Counter">
          <p>
            FC Counter:
            {state.counter}
          </p>
          <button type="button" onClick={() => actions.addToCounter(1, addedCounter)}>
            +1 to global
          </button>
        </div>
      );
    };

    export default Counter;
```

The greatest of it all is that it all is fully typed! Full intellisense :-) Get the code here: [https://github.com/GaryWenneker/react-hookz](https://github.com/GaryWenneker/react-hookz) or pull down the NPM package: [https://www.npmjs.com/package/react-hookz](https://www.npmjs.com/package/react-hookz)

<figure class="kg-image-card kg-width-full">![](/assets/img/uploads/2020/02/wu0ZiW2yRSVSE-1-.gif)</figure>

## Examples

*   [React Hookz minimal example (like usage above)](https://codesandbox.io/s/react-hookz-global-state-vl5x7)
*   [React Hookz HOC](https://codesandbox.io/s/react-hookz-hoc-112fy)
*   [React Hookz Fetch Action](https://codesandbox.io/s/react-hookz-fetch-action-demo-ellw3)
*   [Using React Hookz in a class component](https://codesandbox.io/s/react-hookz-class-component-hfimj)