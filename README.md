# Front end Repo Standards

The goal of this documentation is to create a standard around different aspects of an application. Although it is mainly oriented around Create React App, many principles can be applied around other React frameworks.

To best implement these standards **absolute imports** are encouraged. This is easily handled in the **jsonconfig.json** or **tsconfig.json** files. [React DOCS on absolute imports](https://create-react-app.dev/docs/importing-a-component/#absolute-imports)

## Documentation Sections

1. [Folder and File Structure](./folderAndFileStructure/folderAndFileStructure.md)

This holds information related to folder structure, component placement, and file sturcture. This aspect is the least useable in other frameworks that have file system routing such as NextJS. The goal of this section is to handle component placement in way where component tracking can be easier and avoid having single use components dumped into the **src/components** directory. This method uses two folders **components**, and **sections** to handle the filesystem. These folders can be used in multiple levels in the file-tree depending on a set of rules. Details of the implementation can be found in the **folderStructure** directory.

2. [Services](./services/services.md)

This holds information related to API calls and third party service integrations.

3. Utils

Utils are files that hold utility functions that can be used across the repository, such as date format utils, general utils, or utils for processing data from certain parts of your application.

Utils files will be located in **src/utils/**

These files will follow the naming convention: `utilCategoryUtils.js/.ts`

4. [Redux](./redux/redux-general.md)

This section holds information related to Redux such as folder structure, file names, implementation standards, and base guides for base redux functionality and Redux Thunk.

Feel free to add changes or suggestions to these standards.

## General Repo Standards

Due to the flexibility offered by JavaScript/TypeScript in order to set uniformity in syntax these standards are encouraged accross all sections of the project and documentation and should be looked at durin code reviews and pull requests.

- JavaScript/TypeScript files that don't have JSX/TSX syntax sholud have the .js/.ts extension.

- ReactJS/ReactTS files will use the .jsx/.tsx extension. (This makes no significant difference on an IDE other than a logo, but it is semantic and allows us to infer the nature of the file without opening it)

- ES6 Arrow Function syntax will be used for components and functions used in the repository. [MDN Arrow Function Expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

- Async/Await with try/catch will be favored over .then/.catch with one exception: Using a simple API call inside a logicless useEffect. **callbacks** will not be used.

- const/let will be used, no var will be used. All objects/arrays will be set as **const**. The use of **const** is greatly encouraged over let.

- Functional components will be used over Class components on every case except the **Error Boundary** component which can only be done with a class component.

- Component state will be managed using hooks. [React Hooks Overview](https://reactjs.org/docs/hooks-overview.html)

- State shared between 2 or more components that requires state to be moved up the component tree will be added to Redux. State that travels down between more than 2 components will be sent to Redux as well to avoid prop-drilling.

- The use of the array methods map, filter, slice, and reduce, are highly encouraged over the use of for loops to avoid mutations that may result in unexpected behavior and to be clearer over the intention of the logic being and improve readability:

**map** implies transformation of the data through all items of an array. **filter** implies the filtering of items in an array. **slice** implies the non-destructive slicing of an array. **reduce** implies the use of data in an array to create new data or a change in structure, such as data normalization.

-. Push, unshift, shift, pop are not to be used and should be replaced with **spread syntax equivalents** or **slice**. [MDN Spread Syntax Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

These alternatives are non-destructive and are a safeguard against reference induced rendering errors.

**push with spread syntax**

```javascript
const newArray = [...arr1, value];
```

**unshift with spread syntax**

```javascript
const newArray = [value, ...arr1];
```

**shift with slice**

```javascript
const newArray = array.slice(1);
```

**shift with spread syntax**

```javascript
const [firstItem, ...newArray] = array;
```

**pop with slice**

```javascript
const newArray = array.slice(0, array.length - 1);
```

- **Spread syntax** will be used when working with objects. **Object.assign()** and **delete** will not be used. [MDN Spread Syntax Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

**Object.assign() with spread syntax**

```javascript
const obj1 = {
  propertyName: 'test',
  area: 10,
  antiquity: 20,
};

const obj2 = {
  city: 'Bogota',
  area: 25,
};

// Objects will be merged into a new object
// Overlapping props in obj1 and obj2 will be overriden by the values in obj2
const newObj = { ...obj1, ...obj2 };

// Resulting object
newObj = {
  propertyName: 'test',
  antiquity: 20,
  city: 'Bogota',
  area: 25,
};
```

**delete with spread syntax**

```javascript
// Object delete replacement
const object = {
  propertyName: 'test',
  antiquity: 20,
  city: 'Bogota',
  area: 25,
};

// List props to remove
const { city, area, ...newObj } = object;

newObj = {
  propertyName: 'test',
  antiquity: 20,
};
```

**delete with spread syntax using dynamic values**

```javascript
const object = {
  propertyName: 'test',
  antiquity: 20,
  city: 'Bogota',
  area: 25,
};

// General util to non-destructively remove multiple object props
const removeProps = (object, ...props) => {
  const newObject = props.reduce((accumulator, prop) => {
    const { [prop]: ignore, ...rest } = accumulator;
    return rest;
  }, object);
  return newObject;
};

// Inline version
const removePropsInline = (object, ...props) =>
  props.reduce((accumulator, prop) => {
    const { [prop]: ignore, ...rest } = accumulator;
    return rest;
  }, object);

const newObj = removeProps(object, 'area', 'city');

newObj = {
  propertyName: 'test',
  antiquity: 20,
};
```

- The use of ?? will be favored over || in variable declaration and should replace a ternary when possible: `const buttonLabel = label ?? 'submit'` over `const buttonLabel = label ? label : 'subimt'` [Nullish coalescing operator DOCS](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)

- Ternaries will only be used for variable declarations or conditional rendering and can only hold a single if/else statement for readability: `const activeClass = active ? 'active' : ''` [Ternary Operator DOCS](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)

If handling multiple if/else statements the use of **if/else** or **swith/case** should be considered as an alternative.

- Conditional rendering using a ternary that returns null should be replaced with && [Logical AND Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND):

```jsx
// And
{
  loading && <Spinner />;
}
// Ternary
{
  loading ? <Spinner /> : null;
}
```

- Optional chaining should be use to avoid excessive && operators [Optional Chaining Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining):

```javascript
// Optional Chaining
data?.user.?property?.map(...);

// && chaining
data && data.user && data.user.property && data.user.property.map(...);
```

- Inline ifs can only hold single statements.

- To avoid excessive if nesting use an inverse condition when using an **if statement** that envelops all the logic of a code block:

```javascript
// Inverse condition
useEffect(() => {
  if (user !== 'admin') return;
  // Admin logic
}, []);

// Single condition useEffect if
useEffect(() => {
  if (user === 'admin') {
    // Admin logic
  }
}, []);
```
