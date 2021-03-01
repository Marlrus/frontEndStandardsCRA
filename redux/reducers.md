# Reducers

[Back to Redux](./redux-general.md)

**Location**: src/redux/stateName/

## Reducer file name

stateName.reducer.js

Example: user.reducer.js

## Index

1. [Reducer Structure](#reducer-structure)
2. [Reducer](#reducer)
3. [States of a Promise](#states-of-a-promise)
4. [Pagination and Infinite Scroll](#pagination-and-infinite-scroll)
5. [Root Reducer](#root-reducer)

## Reducer Structure

[index](#index)

The Reducer intercepts actions and manages state changes in by **case**. They hold the initial value for the slice of state they are handling in the constant **INITIAL_STATE** and import actions from the actions file.

### Initial State

To make modifying the state easier and seeing how the state looks when creating a case in opr reducer, we have this constant on our reducer file.

```javascript
const INITIAL_STATE = {
  userData: {
    name: '',
    id: '',
  },
};
```

### Reducer

[index](#index)

The reducer intercepts an action and exectues logic related to it. It **always** has to have a **default case** that returns state to handle unrelated actions.

```javascript
const userReducer = (state = INITIAL_STATE, { type, payload }) => {
  switch (type) {
    case UserActionTypes.SET_USER_DATA:
      return payload;
    // Other cases
    default:
      return state;
  }
};

export default userReducer;
```

### Reducers with Thunk

When there is state set after an API call we have to manage the different states of a **Promise**: Pending, resolved, rejected.

```javascript
const INITIAL_STATE = {
  pending: false,
  userData: {
    name: '',
    id: '',
  },
  error: undefined,
};
```

#### States of a Promise

[index](#index)

A promise is a an object that _represents the eventual completion, or failure, of an asynchronous operation and its resulting value._ A promise has 3 states that we must handle in our reducers that use Thunk.

1. Pending

We set **pending** true.

2. Rejected

We set **pending** to false and send the error message as payload to the **error** property to handle the error case.

3. Resolved/Fulfiled

We set **pending** to false and send the data related to the response to the payload to be set in our state.

#### Pagination and Infinite scroll

To access pagination data and handle requests, we create a state to save this data in INITIAL_STATE.

```javascript
const INITIAL_STATE = {
  pending: false,
  userData: {
    name: '',
    id: '',
  },
  userPagination: {
    page: 1,
    items: 10,
  },
  error: undefined,
};
```

## Root Reducer

[index](#index)

The root-reducer is where we combine every slice of state from our reducers which determines the structure of our store. We keep this file separate from the stare in case that we want to presist a reducer or add an **enhancer** to any reducer.

```javascript
import { combineReducers } from 'redux';

import userReducer from './reducers/user.reducer';

const rootReducer = combineReducers({
  user: userReducer,
});

export default rootReducer;
```
