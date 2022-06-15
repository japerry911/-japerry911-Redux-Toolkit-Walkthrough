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
    - `action creators`
      - 