# Redux Thunk

[Back to Redux](./redux-general.md)

**location**: src/redux/stateName/stateName.actions

Thunk is middlewar designed to manage asynchronous action related to state used by Redux. It is generally used for **get** operations. Thunks are **action creators** that dispatch multiple actions depending on the state of a **Promise** in an API request. The convention is to use actions with **START, SUCCESS, and FAILURE** names.

Thunk allows us to handle how a component is rendered while an API action is being processed. With the **pending** state we created we can display a loader, desable a button, or disable a useEffect.

Thunks are handled in the **actions** file.

## Index

1. [Thunk without parameters](#thunk-without-parameters)
2. [Thunk with parameters](#thunk-with-parameters)
3. [Thunk with pagination in Redux](#thunk-with-pagination-in-redux)
4. [Using a Thunk](#using-a-thunk)
5. [Debouncing with Thunk](#debouncing-with-thunk)

## Actions used by thunk

These action are not used directly by our component, therefore these actions don't need to have the **Action** word added at the end because there wont be a name collision in the component.

## Thunk without parameters

[index](#index)

```javascript
export const getUserAction = () => async (dispatch, getState) => {
  // getState() retruns the whole state in our store
  const userPagination = getState().user.userPagination;
  // dispatch fires the action for each state of our promise
  dispatch(userFetchStart());
  try {
    const res = await getUser();
    dispatch(userFetchSuccess(res.data));
  } catch (err) {
    dispatch(userFetchFailure(err.message));
  }
};
```

## Thunk with parameters

[index](#index)

A thunk can take parameters that are used in our API service. These parameters are sent to in our component in the same way we would using a normal function. This way we can handle pagination or parameters in our requests if they are managed locally in our component.

```javascript
const getUserByIdAction = userId => async dispatch => {
  dispatch(userByIdFetchStartAction());
  try {
    const res = await getUserById(userId);
    dispatch(userByIdFetchSuccess(res.data));
  } catch (err) {
    dispatch(userByIdFetchFailure(err.message));
  }
};
```

## Thunk with pagination in Redux

[index](#index)

If we are saving the pagination state in Redux, we can use getState, if we handle it externally we can use a thunk with parameters.

```javascript
export const getUserAction = () => async (dispatch, getState) => {
  // getState() retruns the whole state in our store
  const userPagination = getState().user.userPagination;
  dispatch(usersFetchStart());
  try {
    const res = await getUser(userPagination);
    dispatch(usersFetchSuccess(res.data));
  } catch (err) {
    dispatch(usersFetchFailure(err.message));
  }
};
```

## Using a Thunk

[index](#index)

To use a thunk, we import the action and connect it via **mapDispatchToProps** as a normal action.

```javascript
import { getUserAction } from 'redux/user/user.actions';

useEffect(() => {
  getUser();
}, [getUser]);

const mapDispatchToProps = dispatch => ({
  getUser: () => dispatch(getUserAction()),
});
```

This action will _dispatch_ all other actions ad we can handle different cases from our Redux store.

## Debouncing with Thunk

[index](#index)

Sometimes we need to fire API requests when there is a change in an input. To avoid an API call on each keypress or input change, we can implement **debouncing**. A debounce waits a set amount of time **in milliseconds** after an input has settled to execute the API request.

```javascript
import makeDebounce from 'redux-debounce-thunk';

// Thunk
const getUserByIdThunk = userId => async dispatch => {
  dispatch(userByIdFetchStartAction());
  try {
    const res = await getUserById(userId);
    dispatch(userByIdFetchSuccess(res.data));
  } catch (err) {
    dispatch(userByIdFetchFailure(err.message));
  }
};

// debounced thunk action
export const fetchUserByIdAction = makeDebounce(getUserByIdThunk, 1000);
```

This action will only executhe the API call when the input has been still for 1 second. This is useful yor filters that have fast changing inputs like a **range** input which can generate dozens of changes in less than a second, triggering the useEffect that handles the API call.
