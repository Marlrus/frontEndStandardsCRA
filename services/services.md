# Services

For handling API calls we will use the **services** directory located in **src/services/**

In this directory, services will be separated by API:

**src/services/api_name.js**

If the API has many endpoints, they can be separated in additional directories by category:

**src/services/api_name/category_name.js**

## Services file

**Axios** will be used for our API calls. Services in the same files must share the same **apiKey** and **rootUrl** from the **env-cmdrc** file.

[Axios DOCS](https://github.com/axios/axios)

```javascript
import axios from 'axios';

const apiKey = process.env.REACT_APP_API_NAME_API_KEY;
const rootUrl =
  process.env.REACT_APP_API_BASE_URL + process.env.REACT_APP_API_BASE_PATH;
```

## Services

Services are functions that return a **Promise** that makes our API call. These functions are a wrapper that make our request by sharing our rootUrl and apiKey. Since they will be handled inside a **try/catch** block or **then/catch**, we only need to return the promise created by axios, therefore the function is not an **async** function:

```javascript
export const getUserByEmail = email => {
  const endpointUrl = process.env.REACT_APP_GET_USER_BY_EMAIL;
  const url = rootUrl + endpointUrl;
  const headers = {
    'x-api-key': apiKey,
    'Content-Type': 'application/json',
  };
  const params = {
    mail: email,
    limit: 10,
    offset: 0,
  };
  return axios({
    method: 'get',
    url,
    params,
    headers,
  });
};
```

Using the **axios** function directly is more uniform, however, axios methods can be used as well:

```javascript
return axios.get(url, params, {
  headers: {
    'x-api-key': apiKey,
    'Content-Type': 'application/json',
  },
});
```

Inside our [thunk actions](../redux/thunk.md) or useEffect we will handle the response or error, dispatching actions in Thunk, or managing them using component state.
