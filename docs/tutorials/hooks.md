---
id: hooks
title: 'Tutorial: Hooks API'
hide_title: true
sidebar_label:
---

# Tutorial: Using the Hooks API

:::tip What You'll Learn

- How a Redux store integrates with a UI
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

That said, **Redux was specifically designed to work well with [React](https://reactjs.org)**. React lets you describe your UI as a function of your state, and Redux stores and updates that  state in response to actions.

### Redux and UI Integration

Before we dig into the specifics of using Redux with React, let's quickly review the high level steps for using Redux with a generic user interface. 


Using Redux with any UI layer requires a few consistent steps:

:::info Simple Redux Data Flow
1. Create a Redux [store](https://redux.js.org/understanding/thinking-in-redux/glossary#store)
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

If you want to follow along with the tutorial, please fork our [Stater Template](./needs-link.md) on CodeSandbox. For the completed tutorial code, please see the end of the tutorial.

//THIS ABOVE NEEDS TO BE UPDATED BASED ON FINAL PROJECT CODEBASE re: SNAPSHOT CODEBASE
See [#13](https://github.com/ligabloo/react-redux/issues/13) for more

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


### Creating our first slice

Thinking about your global state as individual "slices" of a larger whole is a great way to make state management more, well… manageable. 

Each slice should correspond to a feature in your application and will hold its state, actions creators for dispatching updates, as well as any [selectors](https://redux.js.org/tutorials/fundamentals/part-2-concepts-data-flow#selectors) that can be used at the component level to reference that state.

`createSlice()` receives an object with three main configuration fields:
- `name`: a string that will be used as the prefix for the actions that createSlice() will generate. This string will represent the `domain` in the `domain/eventListener` convention that createSlice() uses under the hood to dynamically generate "action types"
- `initialState`: representing the starting state of the reducer
- `reducers`: an object comprised of `reducer` functions that update global state

**Let's define our `todoSlice` now:**

:::important A note on file structure:
We recommend a hybrid approach between the "Feature folders" / "Domain"-style file structure and a "Ducks/Slices" approach. Components and `slice` files are stored in a `feature` directory, with actions/reducer logic stored in a single `featureSlice` file.  

For more on file structure, check out the [FAQ](https://redux.js.org/faq/code-structure)
:::

// ! NEED TO REFACTOR THIS, AS THE FEATURE DIRECTORY WILL ALREADY BE IN PLACE IN THE STARTER CODEBASE ! //

Start by defining a `features` directory inside of `src`. Create a `todos` directory inside of `features` and add a file called `todosSlice.js`.

```js
//todosSlice.js 

import { createSlice } from "@reduxjs/toolkit";

const initialState = [];

const todosSlice = createSlice({
  name: "todos", //The 'domain'
  initialState,
  reducers:{},
})

export default todosSlice.reducer
```

While this slice doesn't **do** anything yet, we have scaffolded out the default parts of any Redux slice:

1. An `initialState` object. (_Remember, this only represents this state of slice, not the total Redux state tree._)
2. Your slice, returned from RTK's [createSlice()](https://redux-toolkit.js.org/usage/usage-guide#simplifying-slices-with-createslice) method
3. The `todosSlice.reducer`, which is exported and combined with our other slice reducers via the `rootReducer` argument and `createStore` 

### Connecting to the store

Back in `store.js`, its time to wire up our slice. 

```js {3,7}
//store.js
import { configureStore } from "@reduxjs/toolkit";
import todosReducer from "./features/todos/todosSlice"

const store = configureStore({
  reducer: {
    todos: todosReducer
  },
});

export default store;
```
We start by importing our `todosReducer`, which we just created in `todosSlice`. We then pass that to the `reducer` field in `configureStore`. The functions passed to `reducer` make up our `Root Reducer`, which is ultimately responsible for updating global state.

### Adding a Reducer
Now that our slice and store are connected, its time to define our first slice reducer. 

```js {5,11,12,13,14,15,16,21}
//todosSlice.js 
import { createSlice } from "@reduxjs/toolkit";

const initialState = [];
let nextTodoId = 0;

const todosSlice = createSlice({
  name: "todos", //The 'domain'
  initialState,
  reducers:{
    todoAdded(state, action){
      state.push({
        id: ++nextTodoId,
        content: action.payload,
        isCompleted: false
      });
    }
  },
})

export const { todoAdded } = todosSlice.actions
export default todosSlice.reducer
```

In defining out reducer, we've addeded several things to our slice. First is `nextTodoId`, a variable that allows us to add a unique numerical id to each todo item in our state. 

Next we've defined `todoAdded` in our `reducers` field. Look [here](https://redux-toolkit.js.org/api/createSlice#parameters) for more on how the `reducers` field works as a "builder callback" to add reducers to the slice. 

You may notice that in defining how our reducer updates state, we appear to be pushing our new value directly onto our `initialState` array and that state in [Redux should always be updated immutably](https://redux.js.org/understanding/thinking-in-redux/three-principles#changes-are-made-with-pure-functions).

In this case, because we are defining our slice using Redux Toolkit, we can write mutating state logic. That's because RTK uses [Immer](https://immerjs.github.io/immer/) under-the-hood to handle state tree updates and apply those updates immutably.

Finally we are exporting the `todoAdded` action creator from the slice's `actions` object. This function, while sharing a name with our `todoAdded` reducer, was generated via `createAction`. Destructuring the individual action creator and exporting it allows us to reference it more clearly at the component level. 

### Testing our Reducer

Before we dispatch our action from the UI layer, let's check to see that everything is working correctly. We can call our `todoAdded` action from `index.js` and confirm that everything is working as expected with [Redux Dev Tools](https://redux-toolkit.js.org/usage/usage-guide#simplifying-store-setup-with-configurestore).

```js {8,15}
//index.js
import React from "react";
import ReactDOM from "react-dom";

import { Provider } from "react-redux";
import store from "./store";

import { todoAdded } from "./features/todos/todosSlice" 
// This action is destructured from todosSlice.actions,
// a field that is automatically populated using
// the reducers passed to `createSlice`

import TodoApp from "./TodoApp";

store.dispatch(todoAdded("Test todoAdded reducer")) 
// We will use the hooks version `useDispatch` 
// at the component level

const rootElement = document.getElementById("root");
ReactDOM.render(
  <Provider store={store}>
    <TodoApp />
  </Provider>,
  rootElement
);
```

// ! ADD A SECTION ABOUT REDUX DEV TOOLS HERE. How do we access via Code Sandbox? ! //

//! REFACTOR THE FOLLOWING TO HAVE USER IMPORT `store` AND SETUP `<Provider >` HOC !//
Also, please note that `<TodoApp />` is wrapped in the `<Provider>` higher-order component.
This provider does exactly what it's name implies: _it provides all of its children components with access to the Redux store._ Now let's jump to the component level and see how we can access the store to add new items to our todo list. 

:::tip 
Now that we've confirmed that its working, you can remove the call to `todoAdded` as well as the associated import. 
:::

## Handling an Event with useDispatch

We know that our `todoAdded` reducer works the way we intended and we haven't even used it in our UI yet! This predictability, the confidence that comes with knowing your state is going to change the way you expected is one of Redux's core strengths. 

Now let's explore how to leverage that confidence as we dispatch an action in response to a UI event, in this case, submitting the text of a `todo` item. 

```js {3,4,7,16}
//AddTodo.js
import { useState } from "react";
import { useDispatch } from "react-redux";
import { todoAdded } from "./todosSlice";

const AddTodo = () => {
  const dispatch = useDispatch();
  const [input, setInput] = useState("");

  const handleUpdateInput = (e) => {
    setInput(e.target.value);
  };

  const handleSubmit = (e) => {
    e.preventDefault(); 
    dispatch(todoAdded(input));
    setInput("");
  };

  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleUpdateInput} value={input} />
      <button type="submit" className="add-todo">
        Add Todo
      </button>
    </form>
  );
};

export default AddTodo;
```

Now, when we click the `Add Todo` button our `handleSubmit` function calls the `useDispatch` hook, seen here as just `dispatch`. Dispatch takes one argument, the `todoAdded` action, which passes the value from `input` its payload.

Like before, you can confirm that your global state has been updated via Redux Dev Tools. 

## Reading from Global State with useSelector

Up to this point, whenever we've added a new `todo` to our global state, the only way to confirm that everything worked was by checking Redux Dev Tools. Let's fix that by reading from our store at the component level and subscribing to updates.

```js {2,6}
// TodoList.js
import { useSelector } from "react-redux";
import Todo from "./Todo";

const TodoList = () => {
  const todos = useSelector(state => state.todos);

  return (
    <ul className="todo-list">
      {todos && todos.length
        ? todos.map((todo, index) => {
            return <Todo key={`todo-${todo.id}`} todo={todo} />;
          })
        : "You're all done, yay!"}
    </ul>
  );
};

export default TodoList;
```
Again, we start by importing `useSelector` from React Redux. Like `useDispatch`, this hook is able to access this global state `store` from the component level. `useSelector` can access the global `state` value and return the desired information, in this example our array of `todo` items from the `state.todos` field. 

In addition to returning the values from global state when the component loads, `useSelector` also automatically subscribes to that value and listens for updates. When an action is dispatched to the store, `useSelector` will perform an equality check on the piece of state that it is concerned with. If a difference is found the component will force a re-render and return the updated value. 

:::important 
A full discussion of `useSelector`'s equality checking is outside of the scope of this tutorial. To learn more, see [Equality Comparisons and Updates](https://react-redux.js.org/api/hooks#equality-comparisons-and-updates);
:::





