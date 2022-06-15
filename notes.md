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