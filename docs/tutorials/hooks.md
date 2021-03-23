---
id: hooks
title: 'Tutorial: Hooks API'
hide_title: true
sidebar_label:
---

# Tutorial: Using the Hooks API

:::tip What You'll Learn

- How a Redux store works with a UI
- How to use Redux with React Hooks

:::

## Introduction

This tutorial will cover the basics of creating a Redux store, connecting that store to React components, and dispatching actions back to that store, all using [React Hooks](https://reactjs.org/docs/hooks-intro.html). This tutorial is designed to provide a primer for using React Redux with Hooks and shouldn't be thought of as comprehensive. 

For a deeper dive into the concepts discussed here, check the official [Redux Essentials](https://redux.js.org/tutorials/essentials/part-1-overview-concepts) tutorial.

## How to Read This Tutorial

This tutorial is designed to showcase what we consider the best approach to using Redux in a React project. We've tried to keep these explanations beginner-friendly, but we do need to make some assumptions about what you already know:

:::important Prerequisites
  - Familiarity with [HTML & CSS](https://internetingishard.com/).
  - Familiarity with [ES6 syntax and features](https://www.taniarascia.com/es6-syntax-and-feature-overview/)
  - Knowledge of React terminology: [JSX](https://reactjs.org/docs/introducing-jsx.html), [State](https://reactjs.org/docs/state-and-lifecycle.html), [Function Components, Props](https://reactjs.org/docs/components-and-props.html), and [Hooks](https://reactjs.org/docs/hooks-intro.html)
:::

This tutorial, in an attempt to showcase what we think are React Redux best practices, makes use of [Redux Toolkit](https://github.com/reduxjs/redux-toolkit) (RTK), which is intended to be the standard way to write Redux logic.  

:::tip
To learn more about using React Redux without RTK, please visit the [Redux Fundamentals](https://redux.js.org/tutorials/fundamentals/part-1-overview) tutorial.
:::
## Redux - The Basics

Redux is a standalone JS library; this means you can create and use a Redux store even if you don't have a user interface set up. This also means that **you can use Redux with any UI framework** (or even without _any_ UI framework), and use it on both client and server. You can write Redux apps with React, Vue, Angular, Ember, jQuery, or vanilla JavaScript.

That said, **Redux was specifically designed to work well with [React](https://reactjs.org)**. React lets you describe your UI as a function of your state, and Redux contains state and updates it in response to actions.

### Redux and UI Integration

Before we dig into our project, let's quickly review the high level steps for using Redux with a user interface. 


Using Redux with any UI layer requires a few consistent steps:

:::info Simple Redux Data Flow
1. Create a Redux store
2. Subscribe to updates
3. Inside the subscription callback:
   1. Get the current store state
   2. Extract the data needed by this piece of UI
   3. Update the UI with the data
4. If necessary, render the UI with initial state
5. Respond to UI inputs by dispatching Redux actions
:::

This tutorial attempts to follow those basics steps as an outline.
## Project Overview
Before we start writing our code, let's define the requirements for our application. In this tutorial we are building a basic Todo List manager. 

:::info Todo List Requirements
  - Users should be able to input a new Todo item 
  - Todo items should be displayed as a list
  - Users should be able to toggle a Todo items between a `Completed` and `Not Completed` state
  - Users should be able to filter which Todo items they see, showing both completed and incomplete tasks as well as `all` tasks, regardless of status
:::

### Setting Up Your Local Project
If you want to follow along with the tutorial, please fork the [Stater Template](./needs-link.md) on CodeSandbox. For the completed tutorial code, please see the end of the tutorial.

## Todo List Application

### Creating Your Redux Store 
The first step to in any Redux project is creating your [Store](https://redux.js.org/api/store). The Redux Store is a JavaScript object that holds our applications' [State](https://redux.js.org/understanding/thinking-in-redux/glossary#state).

To begin, in the `/src` directory of your project create a `store.js` file. This is where we will define our Redux store. 

:::important `configureStore()` 
Using the RTK `configureStore()` method allows us to quickly setup a Redux store while also providing an improved developer experience versus the default `createStore()` method.  
:::

```js
//store.js

import { configureStore } from "@reduxjs/toolkit";

const store = configureStore({
  reducer: {},
});

export default store;
```

After importing the `configureStore()` method from RTK we can provide several configuration objects to help define our Redux store. The first, and most important for our purposes, is the `reducer` object. Once we define some [Reducer](https://redux.js.org/understanding/thinking-in-redux/glossary#reducer) functions, we will add them as fields here.

### Creating a `slice`

Thinking about your global state as individual "slices" of a larger whole is a great way to make state management more, well… manageable. 

Each slice should correspond to a feature in your application and will hold that feature's state, actions needed for updates, as well as any "selector" functions that can be used at the component level to reference that state.

`createSlice()` receives an object with three main configuration fields:
- `name`: a string that will be used as the prefix for the actions that createSlice() will generate. This string will represent the `domain` in the `domain/eventListener` convention that createSlice() uses under the hood to dynamically generate "action types"
- `initialState`: representing the starting state of the reducer
- `reducers`: an object comprised of `reducer` functions that update global state

Let's define our `todoSlice` now:




