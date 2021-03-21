---
id: hooks
title: 'Tutorial: Hooks API'
hide_title: true
sidebar_label:
---

# Tutorial: Using the `hooks` API

:::tip What You'll Learn

- How a Redux store works with a UI
- How to use Redux with React Hooks

:::

## Introduction

This tutorial will cover the basics of creating a Redux store, connecting that store to React components, and dispatching actions back to that store, all using React `hooks`. This tutorial is designed to provide a primer for using React-Redux with `hooks` and shouldn't be thought of as comprehensive. 

For a deeper dive into the concepts discussed here, check the official [Redux Essentials](https://redux.js.org/tutorials/essentials/part-1-overview-concepts) tutorial.

## How to Read This Tutorial

This tutorial is designed to showcase what we consider the best approach to using Redux in a React project. We've tried to keep these explanations beginner-friendly, but we do need to make some assumptions about what you already know:

:::important Prerequisites
  - Familiarity with [HTML & CSS](https://internetingishard.com/).
  - Familiarity with [ES6 syntax and features](https://www.taniarascia.com/es6-syntax-and-feature-overview/)
  - Knowledge of React terminology: [JSX](https://reactjs.org/docs/introducing-jsx.html), [State](https://reactjs.org/docs/state-and-lifecycle.html), [Function Components, Props](https://reactjs.org/docs/components-and-props.html), and [Hooks](https://reactjs.org/docs/hooks-intro.html)
:::

This tutorial, in an attempt to showcase what we think are React-Redux best practices, makes use of [Redux Toolkit](https://github.com/reduxjs/redux-toolkit)(RTX), which is intended to be the standard way to write Redux logic.  

:::tip
To learn more about using React-Redux without RTX, please visit the [Redux Fundamentals](https://redux.js.org/tutorials/fundamentals/part-1-overview) tutorial.
:::
## React-Redux UI Integration - The Basics

Redux is a standalone JS library; this means you can create and use a Redux store even if you don't have a user interface set up. This also means that **you can use Redux with any UI framework** (or even without _any_ UI framework), and use it on both client and server. You can write Redux apps with React, Vue, Angular, Ember, jQuery, or vanilla JavaScript.

That said, **Redux was specifically designed to work well with [React](https://reactjs.org)**. React lets you describe your UI as a function of your state, and Redux contains state and updates it in response to actions.

### Basic Redux and UI Integration

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
