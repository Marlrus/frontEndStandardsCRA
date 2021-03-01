# Styles and Assets

[Back to Main Docs](../README.md)

In a CRA application, global styles can be added to the App.css file, which can also be made .scss.

There are some styles that will be shared across the application. To make this easier and have a place where values can be changed for the whole repository from a single variable, these will kept in the **StylesConstants.js/.ts** file.

Since absolute imports will be used, these can be imported by simply using the "StylesConstants" string in any import anywhere in the application.

## Index

1. [StylesConstants](#stylesconstants)
2. [Icons and Logos](#icons-and-logos)
3. [Styling SVG Components](#styling-svg-components)
4. [Non SVG Image Assets](#non-svg-image-assets)

## StylesConstants

[index](#index)

**Location:** src/StylesConstants.js

The contents of this file are:

- Color codes stored as strings.
- Size breakpoints.
- Media queries that use the size breakpoints.
- Font families may also be added here which use the fonts in the global styles file.

```javascript
//size
export const size = {
  mobile: '414px',
  tablet: '744px',
  tabletBigger: '1024px',
  desktop: '1280px',
  desktopBigger: '1440px',
  desktopMaxBigger: '2048px',
};

export const maxWidthQueries = {
  mobile: { query: `(max-width: ${size.mobile})` },
  tablet: { query: `(max-width: ${size.tablet})` },
  desktop: { query: `(max-width: ${size.desktop})` },
};

export const minWidthQueries = {
  mobile: { query: `(min-width: ${size.mobile})` },
  tablet: { query: `(min-width: ${size.tablet})` },
  desktop: { query: `(min-width: ${size.desktop})` },
};

//PURPLE PALETTE
export const electricPurple900 = '#190033';
export const electricPurple800 = '#320066';
export const electricPurple700 = '#4A0199';
export const electricPurple600 = '#6301CC';
export const electricPurple500 = '#7C01FF';
export const electricPurple400 = '#9634FF';
export const electricPurple300 = '#B067FF';
export const electricPurple200 = '#CB99FF';
export const electricPurple100 = '#E5CCFF';
export const electricPurple000 = '#F2E5F5';

// Font-family muli
export const muliRegular = 'Muli';
export const muliBoldFamily = 'Muli Bold';
export const muliExtraBoldFamily = 'Muli ExtraBold';
```

## Icons and Logos

[index](#index)

Image assets in react can be imported to get a path to the src. SVGs are very efficient for logos and icons and can be rendered as an HTML tag.

1. src in an image tag
2. Standalone component for SVGs.

As of React 17 the import statement:

`import { ReactComponent as Logo } from 'static/svgs/home.svg'`

Will not create an SVG component and may result in an error. To fix this, the following script can be run in the terminal:

`npx @svgr/cli src/static/svgs --out-dir src/svg --icon`

Here wi will assume that our Svg assets are in **src/static/svgs** and that they will be outputed into a **src/svg** component.

This script converts the SVGs into React Components and creates an index file so that they can be imported easily.

```javascript
import { Notification, Bell, Home, List } from 'svg';

<Notification />;
```

The components can be used as a component.

### Styling SVG Components

[index](#index)

One huge advantage of having SVGs as components is that their colors can be targetted via the **path** property in CSS. This allows us to change the icon color easily and create smooth transitions and animations:

```js
export const SvgContainer = styled.div`
  svg {
    height: 40px;
    width: 40px;
    transition: 0.2s;

    path {
      color: ${white};
    }

    &.active {
      path {
        color: ${electricPurple500};
      }
    }
  }
`;
```

This allows us to dynamically change propreties of an SVG based on class name, props, or anything we might need.

### Non SVG Image Assets

[index](#index)

If the image that you want to use is better suited to an image format such as png, or will be used as a **src** property in an image tag, it should be imported into the **src/Images.js** file.

The contents of this file can be imported using "Images"

Images can be grouped into objects by category that allow easier categorization and usability:

```javascript
// LOGOS
import CompanyLogo from './static/assets/logos/company_logo.svg';
// Icons
import Notification from './static/assets/icons/notifications.svg';
import Bell from './static/assets/icons/bell.svg';
import Home from './static/assets/icons/home.svg';

export const logos = {
  CompanyLogo,
};

export const icons = {
  Notification,
  Bell,
  Home,
};
```

Now these paths can be used in a component easily:

```jsx
import { logos, icons } from 'Images';

// Inside component, works for logos and icons
<img src={logos.CompanyLogo} alt='Company Logo' />;
<img src={icons.Bell} alt='bell icon' />;
```

This facilitates the use of images and imports because image imports are seldom completed by the IDE or LSP and can be nested deep in the **static** directory.
