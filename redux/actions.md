# Actions

**Location**: src/redux/stateName/

Since we will be using absolute imports, anything related to redux can be accessed with the "redux/stateName/" pattern.

## Action file name

nobreDeEstado.actions.js

Exapmle: user.actions.js

## Action file structure

The Action file contains the name of the actions that will be used in the reducer and action funcitions, as well as the action functions, and thunk actions.

### ActionTypes

These are the strings that will be used in the **type** property of our actions and the **case** section of our switch statement in our reducers. If the action type is to be changed, we can replace the value in this object and the change will be reflected in our reducer and actions simultaneously. This process can be done using **F2** in VSCode or using _search and replace_ in your IDE, or the _LSP server_.

Actions **always have the state name of the state being handled to avoid action collisions in our reducers**.

**Name:** stateNameActionTypes

```javascript
export const userActionTypes = {
  USER_FETCH_START: "USER_FETCH_START",
  USER_FETCH_SUCCESS: "USER_FETCH_SUCCESS,
  USER_FETCH_FAILURE: "USER_FETCH_FAILURE",
  SET_USER_DATA: "SET_USER_DATA",
  CLEAR_USER_DATA: "CLEAR_USER_DATA",
  UPDATE_USER_DATA: "UPDATE_USER_DATA",
  REMOVE_USER_DATA: "REMOVE_USER_DATA",
  ADD_USER_DATA: "ADD_USER_DATA",
}
```

Actions mus be _descriptive_ with what the action is doing. For CRUD operations:

- SET: Deletes existing data and overwrites them completely with new data.

```javascript
return payload;
```

- CLEAR: Returns the INITIAL_STATE in the reducer.

```javascript
return INITIAL_STATE;
```

- UPDATE: Adds or changes _some_ values in the existing state.

```javascript
return { ...state, ...payload };
```

- REMOVE: Removes a value from the existing state.

```javascript
// Array
return state.filter(x=> x.id !== payload)
// Object
const { [payload]: ignore, ...rest } = state
const return rest

```

- ADD: Adds items to a state that has an **ARRAY** data structure or **object listed by uid**.

```javascript
// Array
return [...state, ...payload];
// Object por uid
return {
  ...state,
  [payload.id]: { ...payload },
};
```

Actions can also have other names that describe specific actions.

#### Shared Actions

In the case that many actions are handled sequentialy, it is _valid to create an_ **event** _action that is shared between multiple reducers_ to dispatch changes in many parts of the state simultaneously. This action is imported to the reducers that use it and logic belonging to it is handled to avoid multiple sequential actions in our aplications, allowing easier scaling.

This pattern should be considered when you need to fire 2 actions on an event such as the closing of a modal or an API call.

### Action Functions

Actions are functions **that return an object**. The **type** proprety is mandatory, whereas the **payload** property is optional and depends on the action and how it will be handled by the reducer.

```typescript
// Action object
interface ActionObject {
  action: string;
  payload?: any;
}
```

Actions have the name: actionNameAction

```javascript
const addUserDataAction = (userData) => ({
  type: userActionTypes.ADD_USER_DATA,
  payload: userData,
});
```

The word **Action** is added to the name to avoid collisions when using them in components. Since you will want to use the _action name as the prop in the component_ have it being the same as the import can lead to a collision that the IDE will not detect and leads to unexpected hard to debug behavior:

```javascript
import { addUserDataAction } from "redux/user/user.actions";

const mapDispatchToProps = (dispatch) => ({
  addUserData: (userData) => dispatch(addUserDataAction),
});
```
