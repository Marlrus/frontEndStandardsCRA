# Folder and File Structure

A component is the base unit used when creating a React app. Components can be re-usable, however, it is quite often that you have to create components that will only be used for a certain part of your application. If the normal structure of a src/components directory and a src/pages directory you may end up with hundreds of components under the same directory making it very hard to find and track where you need to work.

Standards for file naming are also important in order to keep a uniform naming convention across the repository and to have a clear understanding of where you can find certain logic of a component.

## File Structure

Components live in a directory and the naming is capitalized. There are 4 files that can hold differet parts of the logic from a component:

1. ComponentName.jsx
2. ComponentName.style.jsx
3. ComponentName.container.jsx
4. ComponentName.utils.jsx
5. ComponentName.test.js

### Component File

**ComponentName.jsx**

This is the file where the component is rendered and will be the one used in the imports. This file should contain little to no logic in it, and should be split for clarity if the component is handling more than it should.

This file should follow prop destructuring, this allows for easily adding default values where needed and a possible implicit return statement:

```jsx
const ComponentName = ({
  label,
  value,
  default = 0,
  handleChange,
  ...props
}) => (
  <div>
    <h1>Component logic</h1>
  </div>
);

export default ComponentName;
```

In prop destructuring you can add default values easily with the **=** operator.

#### Imports

Imports should follow the following order to make finding specific import statements easier:

```jsx
// React imports, As of now React is required for Jest testing in React 17
import React, { useState, useEffct } from 'react';
// Enhancer imports
import { withRouter } from 'react-router-dom';
import { connect } from 'react-redux';
// Enhancer tool imports
import { getUserAction } from 'redux/user/user.actions';
// Util imports
import { removeObjectProp } from 'utils/generalUtils';
import { BUTTON_DATA, filterItems } from './ComponentName.utils';

// Component imports
import Button from 'components/Button';
import ListItem from './components/ListItem/ListItem';

// Style Imports
import { ComponentContainer, HeaderContainer } from './ComponentName.style';
```

#### Props and State

Items that come into the component **should not be added to local state**. If data has to be derived or processed form the prop, it can be done so directly in the component:

```jsx
const ComponentName = ({ listItems }) => {
  const firstFiveListItems = listItems.slice(0, 5);
  const totalItemValue = listItems.reduce((total, item) => {
    return total + item.price;
  }, 0);

  return ( ... )
};
```

### Component Styles

**ComponentName.style.jsx**
**ComponentName.style.module.css**
**ComponentName.style.module.scss**

This file contains code related to styles, be it StyledComponents, css modules, or sass modules.

### Container Component

If a component has extensive inner logic, creating a container component is encouraged. This pattern allows the main component to be _dumb_ making it easier to determine the rendering logic by removing clutter.

This pattern should be considered when:

- A component makes many or complex API calls via useEffect.
- A component has multiple pieces of state via useState.
- A component handles a lot of data processing or filtering for the props or state.
- A component has multiple inner functions.

If this pattern is used, all the logic involved should be handled in the container and the props passed down to the actual container should have the format required for rendering.

```jsx
const ComponentNameContainer = props => {
  const { label, listItems, handleClick } = props;

  const [openModal, setOpenModal] = useState(false);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');
  const [userData, setUserData] = useState(false);
  const [showItem, setShowItem] = useState(false);

  useEffect(() => {
    const fetchUserData = async () => {
      try {
        setLoading(true);
        const userRes = await fetchUser();
        setUserData(userRes.data);
        setLoading(false);
      } catch (err) {
        setError(err.message);
        setLoading(false);
      }
    };
    fetchUserData();
  }, [setLoading, fetchUser, setUserData, setError]);

  const setShowItemClass = showItem => (showItem ? 'active' : '');

  return (
    <ComponentName
      loading={loading}
      error={error}
      userData={userData}
      showItem={setShowItemClass(showItem)}
      label={label}
      listItems={listItems}
      handleClick={handleClick}
    />
  );
};
```

### Component Utils

Sometimes may want to have data in the front end to render your components. This is especially useful to keep an open possibility of getting this data from an API to render it in the future where the data structure of the response can be known and data can be easily replaced. This pattern is encouraged.

You might also have heavy duty utils or utils that conditionaly render items in your component such as assets and other components, such as in a form generator. In this case, these utils should live in the utils file.

```jsx
// Capitalized convention lets us know that this is fixed data
const NAV_ITEMS_DATA = [
  {
    label: 'Home',
    path: '/',
    icon: <HomeIcon />,
  },
  {
    label: 'Dashboard',
    path: '/dashboard',
    icon: <DashboardIcon />,
  },
];

// Simplified conditional rendering util
const getInput = inputType => {
  if (inputType === 'text') return <TextInput />;
  if (inputType === 'currency') return <CurrencyInupt />;
  if (inputType === 'date') return <DateInput />;
};
```

### Component Test

To make imports easier in testing, we will have the test file in the component directory. Since we have absolute imports, getting mocks or general components is much easier.
