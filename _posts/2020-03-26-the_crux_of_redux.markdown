---
layout: post
title:      "The Crux of Redux"
date:       2020-03-26 19:19:17 +0000
permalink:  the_crux_of_redux
---

## Working Through Reducers

When I first heard the concept of React-Redux, I loved the idea. Not having to hunt through code to figure out the tangled web of passing props up and down components because we could just access a global state sounded great. But I must admit, finding out how it's actually done threw me for a loop. 

But having spent a few weeks living with Redux, I've gotten my head around how reducers work, and hopefully you will too by then end of this blog.

## Starting With the Basics
Before we get started writing code, we should first think about what we're trying to accomplish. I've definitely been there, where I'm figuring out what I'm trying to do as I'm coding, and it's not a place I'm keen to visit again. 

To properly illustrate the power of reducers, we'd want to use a fairly complicated example, which would make it more difficult to explain and fit the concept into our brains, so we'll have to settle for an example that clearly illustrates how reducers actually work.

To properly show the functionality of Redux, we'll need at least four files--the index file where we set up the store, two different components, and a reducer.

So what do we want to do with 2 components? Let's do something simple--2 buttons that increment or decrement the value on both buttons. The left button will add 1 to the value, the right button will subtract one. 

Let's start with a bit of set up--the index.js file.

### Setting Up the Store
We'll need to import some files in order to get React-Redux working, so we should start with the essentials there. 
`
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import { buttonValueReducer } from './reducers/buttonValueReducer' 
`
React and ReactDOM should be familiar as standard imports in React apps.  The other two will be necessary to get our reducer working correctly--Provider is the component that will essentially hold our store--it provides access to our global state. The createStore function is pretty self explanatory: it creates the store that we can access from in any component.

The last one is importing our custom reducer. We're not ready to implement that yet, but we'll need it to create the store.

How do we implement these things? Let's take a look.

`
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import { buttonValueReducer } from './reducers/buttonValueReducer' 

let store = createStore(buttonValueReducer)
`

The first step is creating the store. We need the reducer we're using to be linked to the store, which is what this statement does. What do we do with this store we've made?

`
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import { buttonValueReducer } from './reducers/buttonValueReducer' 

let store = createStore(buttonValueReducer)

ReactDOM.render(
  <Provider store={store}>
    
  </Provider>,
  document.getElementById('root')
);
`

Hopefully the ReactDOM.render and getting the root element look familiar enough to you as a React developer. Here you can see we pass the store we've made as a prop to the Provider component we've imported from react-redux. As we said before, the Provider is going to give the rest of the app access to the store, so this makes sense.

You have probably noticed that we've left a space in here. That's where the rest of the app goes--often times we'll use an App component here, which you should be familiar with as well. That would look like this:

`
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import { buttonValueReducer } from './reducers/buttonValueReducer'
import App from './App'

let store = createStore(buttonValueReducer)

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
`

But that would add another file to what we're doing, and we want to keep it simple. Let's import our two components directly into the index.js file--let's call them IncrementButton and DecrementButton.

`
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import { buttonValueReducer } from './reducers/buttonValueReducer'
import IncrementButton from './components/IncrementButton'
import DecrementButton from './components/DecrementButton'

let store = createStore(buttonValueReducer)

ReactDOM.render(
  <Provider store={store}>
    <>
      <IncrementButton />
      <DecrementButton />
    </>
  </Provider>,
  document.getElementById('root')
);
`

Now that we've got the setup out of the way, let's take a look at the reducer.
### Using Reducers
At their core, reducers are just functions that take 2 arguments--the state and the action--and return 1 value: the updated state. Now, both the state and the action argument are hashes, so it's not as though it can't get complicated, but that is the essential idea of reducers, in Redux and in general--if you've ever used the reduce() method, you know it's about taking multiple data points and 'cooking it down' to one result.

What we are trying to achieve with reducers in Redux is being able to take in the old state as well as some instructions and data in order to update the state how we'd like. We do this with the use of a switch statement.

When you send an action to a reducer, you'll always include a 'type' key:value pair. The type is what will tell the reducer what kind of action you want it to take.

This is what the basics of that look like:
`
const buttonValueReducer = (state, action) => {
  switch(action.type){
    default:
      return state
  }
}

export default buttonValueReducer;
`

When we're defining our reducer, we should keep in mind what the global state should look like to begin with, what the global state should look like after we have changed it, and the steps we need to take to get there.

For this simple application, our state should be pretty basic. All we are keeping track of is the counter for the buttons. How do we definie the initial values for the state? With default parameters!

`
const buttonValueReducer = (state = { counter: 0 }, action) => {
  switch(action.type){
    default:
      return state
  }
}

export default buttonValueReducer;
`

Redux will automatically run our reducer without arguments to set up the initial state, so by using default parameters, we can ensure that the state starts off with the values we want. We started off our counter at 0, but it could be any number, or even a string for that matter.

Now we have to start working on our switch statement. Right now, all this method is doing is returning what is provided in the state argument, let's start adding some cases.

The typical pattern for the 'type' key:value pair, is that it will be passed as an all uppercase string with underscores for spaces, similar to the way constants are named. In this example, therefore, it would make sense to use the cases, 'INCREMENT_COUNTER' and 'DECREMENT_COUNTER'.

We also should take care that we're returning a new state object, rather than modifying the old state object that was passed in.

This should look like so:

`
const buttonValueReducer = (state = { counter: 0 }, action) => {
  switch(action.type){
    case 'INCREMENT_COUNTER':
      return { ...state, counter: state.counter + 1 }
    case 'DECREMENT_COUNTER':
      return { ...state, counter: state.counter - 1 }
    default:
      return state
  }
}

export default buttonValueReducer;
`
While it's not necessary to do in this simple example, it is good practice to use a spread operator or Object.assign when returning the state, as we want to make sure we preserve the data that we're not manipulating. That way, if we decide we want to make a more complicated reducer, we most likely don't have to change anything with our code for the cases we've already defined. 

And that's it! Our reducer is finished. Now let's make our components and connect them to the reducer.

### Connecting the Components
Unlike the reducer, we will actually need to make some import statements in our components.

We need to use React, and we need some way to connect the component to the reducer. Luckily, React-Redux provides us with just such a function: the connect function.

Our imports will look like this:
`
import React from 'react';
import { connect } from 'react-redux'
`
So far, so good!

Because we're making simple components that don't need their own state, just the global state, we'll stick to using functional components. Let's begin with our IncrementButton:

`
import React from 'react';
import { connect } from 'react-redux'

const incrementButton =  props => {

}

export default connect()(incrementButton)
`

So we can see that the connect function is called during our export. You also might notice that there is an empty set of parentheses before incrementButton--don't worry, we'll begin filling that in shortly. Let's quickly define our component:
`
export default buttonValueReducer;

import React from 'react';
import { connect } from 'react-redux'

const incrementButton =  props => {
  return(
    <button>{props.counter} + 1</button>
  )
}

export default connect()(incrementButton)
`
We've made the button, and we also have put some text on the button: the counter and the indication that we're going to add 1 to it. We're getting the counter from props, and you might be wondering how we're doing that, since you might recall that we didn't pass any props to this component from index.js. This is where connect comes in. 

We're going to use connect to pass properties from the global state to our component through its props! 

In order to do that, we're going to define a function that takes in the global state, and returns an object that will be passed into props. By convention, we'll call this function mapStateToProps.
`
import React from 'react';
import { connect } from 'react-redux'

const incrementButton =  props => {
  return(
    <button>{props.counter} + 1</button>
  )
}

const mapStateToProps = state => ({ counter: state.counter})

export default connect(mapStateToProps)(incrementButton)
`

The connect function will pass the global state to our mapStateToProps function, which will create an object that gets passed back to the connect function, the properties of which, through the magic of React-Redux, will get added to the props of incrementButton.

Now, we're getting information from the global state, but we're not yet giving any information back, and our button doesn't do anything.

In order for our button to do something, we'll need to add an onClick callback. But how do we get the callback to communicate back to the reducer? Another feature of the connect function will let us do this, and we'll have to write another function to use it.

By convention, this method is called mapDispatchToProps, as the dispatch function is what allows us to communicate back to the reducer, and we're going to pass this function into the props of our component so that it has access to it.

Here's how that will look:

`
import React from 'react';
import { connect } from 'react-redux'

const incrementButton =  props => {
  return(
    <button onClick={}>{props.counter} + 1</button>
  )
}

const mapStateToProps = state => ({ counter: state.counter})

const mapDispatchToProps = dispatch => {
  
}

export default connect(mapStateToProps, mapDispatchToProps)(incrementButton)
`

Take note that the mapDispatchToProps is an optional second argument to the connect function, but it must be the second argument, and mapStateToProps must be the first, so if you only wanted to have a mapDispatchToProps function, the export connect would look more like this:

`
export default connect(null, mapDispatchToProps)(incrementButton)
`

Now, what do we put inside the mapDispatch to props function? We are going to return an object that will be mapped to the props of incrementButton, and we want to have access inside incrementButton to a method that will communicate with the reducer. So we'll need to pass in a function!

We have to think back to how we defined our reducer. What do we need to send to it in order to increment the counter? An action with the type "INCREMENT_COUNTER"!

`
import React from 'react';
import { connect } from 'react-redux'

const incrementButton =  props => {
  return(
    <button onClick={}>{props.counter} + 1</button>
  )
}

const mapStateToProps = state => ({ counter: state.counter})

const mapDispatchToProps = dispatch => {
  return {
    incrementCounter: () => dispatch({type: 'INCREMENT_COUNTER'})
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(incrementButton)
`

With this, an incrementCounter function will be passed into the props of incrementButton, which will dispatch an action with type 'INCREMENT_COUNTER' to our reducer. And we've already set up how we're going to call this function: in the onClick callback from the button:
`
import React from 'react';
import { connect } from 'react-redux'

const incrementButton =  props => {
  return(
    <button onClick={() => props.incrementCounter()}>{props.counter} + 1</button>
  )
}

const mapStateToProps = state => ({ counter: state.counter})

const mapDispatchToProps = dispatch => {
  return {
    incrementCounter: () => dispatch({type: 'INCREMENT_COUNTER'})
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(incrementButton)
`

We did it! Now, the other button should look very similar to this one, with minor changes to use decrementing instead.

It should look like this:
`
import React from 'react';
import { connect } from 'react-redux'

const decrementButton =  props => {
  return(
    <button onClick={() => props.decrementCounter()}>{props.counter} - 1</button>
  )
}

const mapStateToProps = state => ({ counter: state.counter})

const mapDispatchToProps = dispatch => {
  return {
    decrementCounter: () => dispatch({type: 'DECREMENT_COUNTER'})
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(decrementButton)
`

In fact, these two components are so similar, you might want to think about merging them into one component that takes props to determine which type of button, but I'll leave that as an exercise for the reader.

After all the work we've done, you might be wondering why not just store the state in the parent component for both of these buttons. But imagine these components are nested deeply in different components of your code--you'd have to pass the data all the way up the chain to a grandparent or great great grandparent, and then all the way back down. If you ever changed the organization of your components, you'd have to make sure to change the how you're controlling the data. 

Redux allows our React components to be much more modular. And that's great!

Happy coding!

