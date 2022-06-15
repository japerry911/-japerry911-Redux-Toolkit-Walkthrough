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

### Application App Structure

- sample project file structure
  - `/src`
    - `index.js` -> app starting point
    - `App.js` -> the top-level React component
    - `/app`
      - `store.js` - creates the redux store instance
    - `/features`
      - `/counter`
        - `Counter.js` - a React component that shows the UI counter feature
        - `counterSlice.js` - the Redux logic for the counter feature
- `redux slices`
  - a slice is a collection of Redux reducer logic and actions for a single feature in your app, typically defined together in a single file
```javascript
import { configureStore } from '@reduxjs/toolkit'
import usersReducer from '../features/users/usersSlice'
import postsReducer from '../features/posts/postsSlice'
import commentsReducer from '../features/comments/commentsSlice'

export default configureStore({
  reducer: {
    users: usersReducer,
    posts: postsReducer,
    comments: commentsReducer
  }
})
```

- creating slice reducers and actions
```javascript
import { createSlice } from '@reduxjs/toolkit'

export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {
      // Redux Toolkit allows us to write "mutating" logic in reducers. It
      // doesn't actually mutate the state because it uses the immer library,
      // which detects changes to a "draft state" and produces a brand new
      // immutable state based off those changes
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    }
  }
})

export const { increment, decrement, incrementByAmount } = counterSlice.actions

export default counterSlice.reducer
```

- `createSlice()`
  - takes care of the work of generating action type strings, action creator functions, and action objects
  - all you have to do is define a name for the slick, write an object that has some reducer functions in it, and it generates the corresponding action code automatically
- rules of reducers
  - must follow some special rules
    - they should only calculate the new state value based on the `state` and `action` arguments
    - they are not allowed to modify the existing `state`. Instead, they must make immutable updates, by copying the existing `state` and making changes to the copied values
    - they must not do any asynchronous logic or other "side effects"
  - why are the rules important?
    - makes your code predictable
      - easier to understand how the code works, and to test it
    - if a function depends on variables outside itself, or behaves randomly, you never know what will happen when you run it.
    - If a function modifies other values, including its arguments, that can change the way the application works unexpectedly. This can be a common source of bugs, such as "I updated my state, but now my UI isn't updating when it should!"
    - Some of the Redux DevTools capabilities depend on having your reducers follow these rules correctly
- reducers and immutable updates
  - in redux, our reducers are **never** allowed to mutate the original/current state values
  - why not mutate state in redux?
    - causes bugs, UI might not update properly
    - makes it harder to write tests
    - makes it harder to understand why and how the state has been updated
    - it breaks the ability to use time-travel debugging correctly
    - it goes against the intended spirit and usage patterns for redux
  - example of returning state without changing the original ones:
```javascript
// ✅ This is safe, because we made a copy
return {
  ...state,
  value: 123
}
```

- `createSlice` function lets you write immutable update an easier way
  - uses a library called `Immer` inside
    - `Immer` uses a special JS tool called `Proxy` to wrap the data you provide, and lets you write code that "mutates" that wrapped data. 
      - `Immer` tracks all the changes you've tried to make, and then uses that list of changes to return a safely immutably updated value
  - example of what code looks like with this:
```javascript
function reducerWithImmer(state, action) {
  state.first.second[action.someId].fourth = action.someValue
}
```

- warning:
  - You can only write "mutating" logic in Redux Toolkit's `createSlice` and `createReducer` because they use Immer inside! If you write mutating logic in reducers without Immer, it will mutate the state and cause bugs!

- Writing Async Logic with Thunks
  - a `thunk` is a specific kind of redux function that can contain asynchronous logic. Thunks are written using two functions:
    - an inside thunk function, which gets `dispatch` and `getState` as arguments
    - the outside creator function, which creates and returns the thunk function
      - example:
```javascript
// The function below is called a thunk and allows us to perform async logic.
// It can be dispatched like a regular action: `dispatch(incrementAsync(10))`.
// This will call the thunk with the `dispatch` function as the first argument.
// Async code can then be executed and other actions can be dispatched
export const incrementAsync = amount => dispatch => {
  setTimeout(() => {
    dispatch(incrementByAmount(amount))
  }, 1000)
}
```

- using thunks require that the `redux-thunk` middleware be added to the redux store when it's created. Redux Toolkit's `configureStore` function already sets it up for us automatically
- AJAX calls example:
```javascript
// the outside "thunk creator" function
const fetchUserById = userId => {
  // the inside "thunk function"
  return async (dispatch, getState) => {
    try {
      // make an async call in the thunk
      const user = await userAPI.fetchById(userId)
      // dispatch an action when we get the response back
      dispatch(userLoaded(user))
    } catch (err) {
      // If something went wrong, handle it here
    }
  }
}
```

- `createAsyncThunk`