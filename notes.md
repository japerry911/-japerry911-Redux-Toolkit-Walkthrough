# General Redux / Redux Toolkit Notes


### What is Redux?
- redux is a pattern and library for managing and updating applciation state, using events called "actions"
- serves as a centralized store for state that needs to be used across your entire application, with rules ensuring that the state can only be updated in a predictable fashion
- why whould I use redux?
  - helps you manage global state
  - easier to understand when, where, why, and how the state in your application is being updated
  - predictable and testable code
- when should I use redux?
  -  useful when:
    - you have large amounts of application state that are needed in many places in the app
    - the app state is updated frequently over time
    - the logic to update that state may be complex
    - the app has a medium or large-sized codebase, many people working on it

### Redux Libraries and Tools

- `React-Redux` 
- `React Toolkit`
  - recommended approach for writing Redux logi
- `Redux DevTools Extension`
  - use in debugging

### Redux Terms and Concepts

 - `state management`
  - one-way data-flow
![one-way data flow diagram](./images/one-way-data-flow.png)
  - simple with one component
  - starts to break down simplicity-wise when more components are added that need to share and use the same state
    - especially if they are located in different parts of the application
      - one way to solve is to have a big view that is a centralized location outside of the component tree
  - idea behind redux
    - a single centralized place to contain the global state in your applciation, and speciifc patterns to follow when updating that state to make the code predictable
  - `immutability`
    - mutable means changeable
    - immutable means it can never be changed
    - JavaScript objects and arrays are all mutable by default
      - can still change even when using `const` keyword
    - in order to update values immutably, your code must make copies of existing objects/arrays, and then modify the copies
    - Redux expects that all state updates are done immutably
  - `actions`
    - an action is a plain JavaScript object that has a `type` field
    - you can think of an action as an event that describes something that happened in the application
    - the `type` field should be a string that gives this action a descriptive name
      - `"domain/eventName"`
      - example
```javascript
const addTodoAction = {
  type: 'todos/todoAdded',
  payload: 'Buy milk'
}
```
- `immutability (contined)`
  - `action creators`
    - a function that creates and returns an action object
      - used so we don't have to write the action object by hand every time
```javascript
const addTodo = text => {
  return {
    type: 'todos/todoAdded',
    payload: text
  }
}
```
- `reducers`
  - a function that receives the current `state` and an `action` object, decides how to update the state if necessary, and returns the new state `(state, action) => newState`. 
  - think of a reducer as an event listener which handles events based on the received action (event) type
  - must always ollow some specific rules:
    - they should only calculate the new state value based on the `state` and `action` arguments
    - they are not allowed to modify the existing `state`. Instead, they must make immutable updates, by copying the existing `state` and making changes to the copied values
    - they must not do any asynchronous logic, calculate random values or cause other "side effects"
  - logic inside the reducer function steps
    - check to see if the reducer cares about this action
      - if so, make a copy of the state, update the copy with new values, and return it
    - otherwise, return existing state unchanged
  - example:
```javascript
const initialState = { value: 0 }

function counterReducer(state = initialState, action) {
  // Check to see if the reducer cares about this action
  if (action.type === 'counter/increment') {
    // If so, make a copy of `state`
    return {
      ...state,
      // and update the copy with the new value
      value: state.value + 1
    }
  }
  // otherwise return the existing state unchanged
  return state
}
```

- `store`
  - current Redux application state live in this object
  - created by passing in a reducer, and has a method called `getState` that returns the current state value
  - example:
```javascript
import { configureStore } from '@reduxjs/toolkit'

const store = configureStore({ reducer: counterReducer })

console.log(store.getState())
// {value: 0}
```

- `dispatch`
  - only way to update the state is to call `store.dispatch()` and pass in an action object
  - the store will run its reducer function and save the new state value inside, and we can call `getState()` to retrieve the updated value
  - dispatching actions can be thought of as triggering an event
  - reducers act as listeners
  - example, we typically call action creators to dispatch the right action
```javascript
const increment = () => {
  return {
    type: 'counter/increment'
  }
}

store.dispatch(increment())

console.log(store.getState())
// {value: 2}
```

- `selectors`
  - functions that know how to extract specific pieces of information from a store state value. As an application grows bigger, this can help avoid repeating logic as different parts of the app need to read the same data:
```javascript
const selectCounterValue = state => state.value

const currentValue = selectCounterValue(store.getState())
console.log(currentValue)
// 2
```

### Redux Application Data Flow
- redux data flow steps
  - initial setup
    - a redux store is created using a root reducer function
    - the store calls the root reducer once, and saves the return value as its initial `state`
    - when the UI is first rendered, UI components access the current state of the redux store, and use that data to decide what to render. They also subscribe to any future store updates so thy can know if the state has changed
  - updates
    - something happens in the app, such as user clicking a button
    - the code dispatches an action to the redux store, like `dispatch({type: 'counter/increment'})`
    - the store runs the reducer function again with the previous `state` and the current `action`, and saves the return value as the new `state`
    - the state notifies all parts of the UI that are subscribed that the store has been updated
    - each UI component that needs data from the store checks to see if the parts of the state they need have changed
    - each component that sees its data has changed forces a re-render with the new data, so it can update whats shown on the screen
![redux application data flow diagram GIF](./images/redux-data-flow.gif)