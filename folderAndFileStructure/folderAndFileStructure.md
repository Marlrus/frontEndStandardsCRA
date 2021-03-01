# Folder and File Structure

[Return to main index](../README.md)

A component is the base unit used when creating a React app. Components can be re-usable, however, it is quite often that you have to create components that will only be used for a certain part of your application. If the normal structure of a src/components directory and a src/pages directory you may end up with hundreds of components under the same directory making it very hard to find and track where you need to work.

Standards for file naming are also important in order to keep a uniform naming convention across the repository and to have a clear understanding of where you can find certain logic of a component.

## Index

1. [File Structure](#file-structure)
2. [Folder Structure](#folder-structure)

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
import React, { useState, useEffect } from 'react';
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

Props that come into the component **should not be added to local state**. If data has to be derived or processed form the prop, it can be done so directly in the component:

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

## Folder Structure

[index](#index)

The motivation of this structure is to handle component directories as self contained entities that make it easier to find components in the place you are working at, removes clutter from the main components directory, and offers an organized way to move and use componenents.

Directories are handled in gropus of two: **components** and **sections**. Optionally, the equivalent **sections** directory in the root src/ directory can be called **pages**. There are multiple **copmonents** and **sections** directories and each directory follows a set of rules that achieve the order of the file-tree.

- **sections** can have nested **components** or **sections** directiories.
- **components** can have nested **components** directories, but _NOT_ **sections** directories.
- Components in any directory can only import files from **components** or **sections** intheir directory and directories higher up in the file tree (parent/grandparent/... directories).
- Components can not access components that are nested in **components** or **sections** in a parent directory (cousin directories).
- Components that are used by two or more components should be moved to the first parent directory that is shared by both components looking to import it.

### Components example

Root components directory example src/components:

```bash
├── components
│   ├── BottomNav
│   │   ├── BottomNav.jsx
│   │   ├── BottomNav.style.jsx
│   │   ├── BottomNav.test.js
│   │   ├── BottomNav.utils.jsx
│   │   └── components
│   │       └── IconNavItem
│   │           ├── IconNavItem.jsx
│   │           ├── IconNavItem.styles.jsx
│   │           └── IconNavItem.test.js
│   ├── ButtonV2
│   │   ├── ButtonV2.jsx
│   │   └── ButtonV2.style.jsx
```

- Only BottomNav has access to IconNavItem, and ButtonV2.
- ButtonV2 has access to BottomNav only
- IconNavItem has access to ButtonV2
- BottomNav and ButtonV2 can be used **anywhere** in the application
- ButtonV2 cannot access IconNavItem

If the IconNavItem component where to be used by ButtonV2 it would be moved up to the root components directory.

In this patterns every component in the root directory is its _self contained_ directory with components only used in it inside. This avoids adding single use or non generic components to the main components directory that will be used throughout the application.

### Sections/Pages exapmle

The following exapmle simulates a migration form the components/ pages/ pattern where the pages directory is not renamed to **sections**.

```bash
├── pages
│   ├── components
│   │   ├── SocialMediaModal
│   │   │   ├── SocialMediaModal.jsx
│   │   │   └── SocialMediaModal.style.jsx
│   ├── Dashboard
│   │   ├── components
│   │   │   ├── DailyCard
│   │   │   │   ├── DailyCard.jsx
│   │   │   │   └── DailyCard.style.jsx
│   │   │   └── VisitSurvey
│   │   │       ├── components
│   │   │       │   └── SurveyState
│   │   │       │       ├── SurveyState.jsx
│   │   │       │       └── SurveyState.style.jsx
│   │   │       ├── VisitSurvey.jsx
│   │   │       └── VisitSurvey.style.jsx
│   │   ├── Dashboard.style.jsx
│   │   ├── Dashboard.jsx
│   │   └── Dashboard.test.js
│   │   └── sections
│   │       └── Profile
│   │           ├── components
│   │           │   ├── FormProfileInfo
│   │           │   │   ├── FormProfileInfo.jsx
│   │           │   │   ├── FormProfileInfo.style.jsx
│   │           │   │   └── FormProfileInfo.test.js
```

**sections** can have **components** and **sections** directories. In this exapmle the Dashboard section has a components/ and sections/ directory.

A section in the src/pages directory usually has it's own route. This holds true for nested sections.

- Dashboard has access to DailyCard, VisitSurvey, Profile, and SocialMediaModal.
- VisitSurvey has access to SurveyState, DailyCard, and SocialMediaModal.
- SocialMediaModal only has access to components from **src/components**.
- Profile has exclusive access to FormProfileInfo, access to DailyCard, VisitSurvey, and SocialMediaModal.
- Dashboard can **not** access SurveyState or FormProfileInfo.

Each section contains components related to its route and components in src/pages/components are shared accross different sections but are not re-usable enough to be moved to src/components.

In this structure we can deduce the following:

- All of the components belonging exclusively to the /dashboard route are located in the directories within it.
- SurveyState is a single use component that is noly used in VisitSurvey.
- SocialMediaModal is used in other sections beyond dashboard.

### Moving Components

If a component can be used between two _brother_ components or many other components. It should be moved to the closest related parent node, or the src/components directory.

In the previous example, if the VisitSurvey component was to be used by multiple sections, it would be moved up into the src/pages/components directory along with its content.

### Complete example

```bash
├── App.jsx
├── components
│   ├── BottomNav
│   │   ├── BottomNav.jsx
│   │   ├── BottomNav.style.jsx
│   │   ├── BottomNav.test.js
│   │   ├── BottomNav.utils.jsx
│   │   └── components
│   │       ├── FloatingButtonNav
│   │       │   ├── FloatingButtonNav.jsx
│   │       │   ├── FloatingButtonNav.styles.jsx
│   │       │   ├── FloatingButtonNav.test.js
│   │       │   └── FloatingButtonNav.utils.js
│   │       └── IconNavItem
│   │           ├── IconNavItem.jsx
│   │           ├── IconNavItem.styles.jsx
│   │           └── IconNavItem.test.js
│   ├── ButtonV2
│   │   ├── ButtonV2.jsx
│   │   └── ButtonV2.style.jsx
├── IconsAndLogos.js
├── index.js
├── index.scss
├── __mocks__
│   ├── providerMocks.js
│   ├── reduxMockInitialState.js
│   └── styleMock.js
├── pages
│   ├── components
│   │   ├── SocialMediaModal
│   │   │   ├── SocialMediaModal.jsx
│   │   │   └── SocialMediaModal.style.jsx
│   ├── Dashboard
│   │   ├── components
│   │   │   ├── DailyCard
│   │   │   │   ├── DailyCard.jsx
│   │   │   │   └── DailyCard.style.jsx
│   │   │   └── VisitSurvey
│   │   │       ├── components
│   │   │       │   ├── SurveyEntry
│   │   │       │   │   ├── SurveyEntry.jsx
│   │   │       │   │   └── SurveyEntry.style.jsx
│   │   │       │   └── SurveyState
│   │   │       │       ├── SurveyState.jsx
│   │   │       │       └── SurveyState.style.jsx
│   │   │       ├── VisitSurvey.jsx
│   │   │       └── VisitSurvey.style.jsx
│   │   ├── Dashboard.style.jsx
│   │   ├── Dashboard.jsx
│   │   └── Dashboard.test.js
│   │   └── sections
│   │       └── Profile
│   │           ├── components
│   │           │   ├── FormProfileInfo
│   │           │   │   ├── FormProfileInfo.jsx
│   │           │   │   ├── FormProfileInfo.style.jsx
│   │           │   │   └── FormProfileInfo.test.js
│   │           │   ├── ProfilePreferences
│   │           │   │   ├── components
│   │           │   │   │   └── Flag
│   │           │   │   │       ├── Flag.jsx
│   │           │   │   │       ├── Flag.style.jsx
│   │           │   │   │       └── Flag.test.js
│   │           │   │   ├── ProfilePreferences.jsx
│   │           │   │   ├── ProfilePreferences.style.jsx
│   │           │   │   └── ProfilePreferences.test.js
│   │           │   └── ProfileSecurity
│   │           │       ├── AccountDeleteForm.jsx
│   │           │       ├── AccountDeleteForm.test.js
│   │           │       ├── PasswordChangeForm.jsx
│   │           │       ├── PasswordChangeForm.test.js
│   │           │       ├── ProfileSecurity.jsx
│   │           │       └── ProfileSecurity.style.jsx
│   │           ├── ProfileContainer.jsx
│   │           ├── Profile.jsx
│   │           └── Profile.style.jsx
```
