# Front end Repo Standards

The goal of this documentation is to create a standard around different aspects of an application. Although it is mainly oriented around Create React App, many principles can be applied around other React frameworks.

To best implement these standards **absolute imports** are encouraged. This is easily handled in the **jsonconfig.json** or **tsconfig.json** files. (React DOCS on absolute imports)[https://create-react-app.dev/docs/importing-a-component/#absolute-imports]

## Documentation Sections

1. Folder and File Structure

This holds information related to folder structure, component placement, and file sturcture. This aspect is the least useable in other frameworks that have file system routing such as NextJS. The goal of this section is to handle component placement in way where component tracking can be easier and avoid having single use components dumped into the **src/components** directory. This method uses two folders **components**, and **sections** to handle the filesystem. These folders can be used in multiple levels in the file-tree depending on a set of rules. Details of the implementation can be found in the **folderStructure** directory.

2. Services

This holds information related to API calls and third party service integrations.

3. Utils

This holds information related to utils.

4. (Redux)[./redux/redux-general.md]

This section holds information related to Redux such as folder structure, file names, implementation standards, and base guides for base redux functionality and Redux Thunk.

Feel free to add changes or suggestions to these standards.

## General Repo Standards

Due to the flexibility offered by JavaScript/TypeScript in order to set uniformity in syntax these standards will be used accross all sections of the project and documentation.

1. JavaScript/TypeScript files that don't have JSX/TSX syntax sholud have the .js/.ts extension.

2. ReactJS/ReactTS files will use the .jsx/.tsx extension. (This makes no significant difference on an IDE other than a logo, but it is semantic and allows us to infer the nature of the file without opening it)

3. ES6 Arrow Function syntax will be used for components and functions used in the repository.

4. Async/Await with try/catch will be favored over .then/.catch with one exception: Using a simple API call inside a logicless useEffect. **callbacks** will not be used.

5. const/let will be used, no var will be used. All objects/arrays will be set as **const**. The use of **const** is greatly encouraged over let.

6. Functional components will be used over Class components on every case except the **Error Boundary** component which can only be done with a class component.

7. Component state will be managed using hooks.

8. State shared between 2 or more components that requires state to be moved up the component tree will be added to Redux. State that travels down between more than 2 components will be sent to Redux as well to avoid prop-drilling.

9. The use of the array methods map, filter, slice, and reduce, are highly encouraged over the use of for loops to avoid mutations that may result in unexpected behavior and to be clearer over the intention of the logic being and improve readability: **map** implies transformation of the data through all items of an array. **filter** implies the filtering of items in an array. **slice** implies the non-destructive slicing of an array. **reduce** implies the use of data in an array to create new data or a change in structure, such as data normalization.

10. Push, unshift, shift, pop are not to be used and should be replaced with **spread syntax equivalents** or **slice**.

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

11. **Spread syntax** will be used when working with objects. **Object.assign()** and **delete** will not be used.

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

// Objects will be merged into a new object, overlapping props in obj1 and obj2 will be overriden by the values in obj2
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

12. The use of ?? will be favored over || in variable declaration and should replace a ternary when possible: `const buttonLabel = label ?? 'submit'` over `const buttonLabel = label ? label : 'subimt'`

13. Ternaries will only be used for variable declarations or conditional rendering and can only hold a single if/else statement for readability: `const activeClass = active ? 'active' : ''`
14. Conditional rendering using a ternary that returns null should be replaced with &&:

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

14. Inline ifs can only hold single statements.

15. To avoid excessive if nesting use an inverse condition when using an **if statement** that envelops all the logic of a code block:

```javascript
// Inverse condition
useEffect(() => {
  if (user !== 'admin') return;
  // Admin logic
});

// Single condition useEffect if
useEffect(() => {
  if (user === 'admin') {
    // Admin logic
  }
});
```
