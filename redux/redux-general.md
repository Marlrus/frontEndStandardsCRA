# Redux standards and configuration

For state management we will use Redux and Redux Thunk. Redux is a state management library that allows moving state between components in any part of an application and it is made out of 3 main parts:

1. Store

The store is an object that contains the state data saved using Redux.

2. [Reducers](./reducers.md)

Reducers handle the logic involved in saving state to the Redux store.

3. [Actions](./actions.md)

Actions send the messages and data to the reducers so that they can be intercepted and handled to be saved in the store.

## [Thunk](./thunk.md)

Redux thunk is middleware for handling async operation with Redux.

## Directory structure and file names

- Root de Redux: **src/redux**
- Store: **src/redux/store.js**
- Root Reducer: **src/redux/root-reducer.js**

Each piece of state will have a directory with its name where actions, reducers, utils, selectors, types, or any related files will live.

- Reducers: **src/redux/stateName/stateName.reducer.js**
- Actions: **src/redux/stateName/stateName.actions.js**

To differentiate the files, we add the kind of file after the file name.

### Action file name

stateName.actions.js

Example: user.actions.js

### Reducers file name

stateName.reducer.js

Exapmle: user.reducer.js

## Store

The store handles storing the data handled by our reducers and inyecting middleware.

```javascript
// Standard store file

import { createStore, applyMiddleware } from 'redux';
import { rootReducer } from './root-reducer';
import logger from 'redux-logger';
import thunk from 'redux-thunk';

const middleware = [thunk];

if (process.env.NODE_ENV === 'development') middleware.push(logger);

const store = createStore(rootReducer, applyMiddleware(...middleware));

export default store;
```
