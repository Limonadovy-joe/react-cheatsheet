# React

- [Fundamentals](#fundamentals)
- [Describing the UI](#describing-the-ui)
  - [Markup](#markup)
  - [What the browser sees](#what-the-browser-sees)
  - [Nesting and organizing components](#nesting-and-organizing-components)
  - [Importing and exporting components](#importing-and-exporting-components)
    - [Barrel exports](#barrel-exports)
    - [Default export vs named export](#default-export-vs-named-export)
  - [Writing markup with JSX](#writing-markup-with-jsx)
    - [The rules of JSX](#the-rules-of-jsx)
  - [JS in JSX with Curly Braces](#js-in-jsx-with-curly-braces)
  - [Passing Props to a Component](#passing-props-to-a-component)
    - [Forwarding props with the JSX spread syntax](#forwarding-props-with-the-jsx-spread-syntax )

## Fundamentals
React is a library. It lets you put components together but it **does not prescribe how to do routing and data fetching**. To build an entire React app you should use a full-stack React framework like Next.js or Remix.

Frameworks that implement React let you fetch data in **asynchronous components that run on the server or even during the build**.

## Describing the UI
React lets you combine your custom markup, CSS and JavaScript into custom components, reuseable UI elements. 

### Markup
Return statements can be written all on one line, as this component:

```tsx
return <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />;
```
But if your markup is not on the same line as return keyword, you must wrap it in a pair of parentheses:

```tsx
return (
  <div>
    <img src="https://i.imgur.com/MK3eW3As.jpg" alt="Katherine Johnson" />
  </div>
);
```

### What the browser sees
Notice the difference in casing:
- ```<section></section> ``` is lowercase, so React knows we refer to a HTML tag
- ```<Profile /> ``` starts with capital letter, so React knows that we want to use our component

### Nesting and organizing components
Because the Profile components are rendered inside Gallery—even several times!— we can say that Gallery is a parent component, rendering each Profile as a “child”.

Components can render other components, but you must never nest their definitions:

```tsx
export default function Gallery() {
  // 🔴 Never define a component inside another component!
  function Profile() {
    // ...
  }
  // ...
}
```
The snippet above is very slow and causes bugs. Instead, define every component at the top level.

When a child component needs some data from a parent, **pass it by props instead of nesting definitions**.

### Importing and exporting components
The magic of components lies in their reusability so you can create components that are composed of other components. But as you nest more and more components, it often makes
sence to start splitting them into different files. 

**A file can not have more that one default export, but it can have as many named exports as you want.**

#### Barrel exports
React imports can get a litle bit verbose:

```tsx
import * as React from "react"
import compose from "recompose/compose"
import type Dispatch from "redux"
import connect from "react-redux"
import querystring from "query-string"
import generateMetaInfo from "shared/generate-meta-info"
import Head from "../../components/head"
import SegmentedControl from "../../components/Layout/segmentedControl"
import ChannelProfileCard from "../../components/Layout/entities"
import CommunityAvatar from "../../components/Layout/avatar"
import ErrorBoundary from "../../components/error"
import MembersList from "./components/MembersList"
import PostFeed from "./components/PostsFeed"
import SidebarSection from "../../components/Layout/SidebarSection"
import CommunitySidebar from "../../components/communitySidebar"
import FeedsContainer from "./style"
```
We can clean up local imports with a pattern called **barrel exporting**.

```tsx
import {
  ChannelProfileCard,
  CommunityAvatar,
  CommunitySidebar,
  ErrorBoundary,
  FeedsContainer,
  FullScreenRedirectView,
  Head,
  InfoContainer,
  MembersList,
  PostFeed,
  SegmentedControl,
  SidebarSection,
} from "../../components"
```
Barrel is about more than the aesthetic though. We get better **intellisence** and **auto-importing for components thanks to the named exports**, along with more flexibility in organizing our filesystem.

**Barrel is a file, where you will re-export all your components in a specific branch of the filesystem.**
```tsx
// src/components/index.js

// This is the Barrel!

export { Error } from "./Error"
export { Row } from "./Layout/blocks/Row"
export { SidebarSection } from "./Layout/SidebarSection"
```

Barrelfile exports come with many gotchas if you want to setup three-shaking to work.
Whether you codebase is affected depends on its complexity and your bundler configuration. Running some tests is the only way to be sure.

Figuring out what code can be safely dropped - **three shaken** is quite complex. Your bundler has to look at each peace of code and figure out if its used anywhere else in any way - **deep scope analysis**.

**The more ambiguous your code or deeper your import trees, the more time it would take to get this right.** So at some point the bundler is just going to tap out and leave the code that isnt sure about in. 


#### Default export vs named export
https://medium.com/@timoxley/named-exports-as-the-default-export-api-670b1b554f65

You should almost always favour named exports, default exports have many downsides:
- difficult to refactor or ensure consistency **since they can be named anything**
- difficult to analyze by **automated tools** or provide **code intellisense  and autocompletion**
- they break tree shaking as instead of **importing the single function you want to use**  you are forcing webpack
to import the entire file whatever other dead code it has **leading to bigger bundle sizes**
- you lose faster/direct access to imports, you lose ES6 module benefits such as tree-shaking


### Writing markup with JSX
JSX is a Javascript extension that allows **creating of DOM trees/nodes using an XML-like syntax.** JSX has been adopted by multiple frameworks. Being a **syntactic sugar**, JSX is generally **transpiled(source-to-source-compiler)** into nested JS functions.

JS expressions can be used inside JSX with curly brackets ``{i === 1 ? 'true': 'false'}``. If-else statements cannot be used inside JSX, but conditional expressions can be used instead.

Code written in JSX requires conversion with a tool such as Babel or TS compiler.

JSX tags map to calls `React.CreateElement()`.

Use lowercase tags <input /> when you need a DOM elems and Capitalized tags for components <Input />.

JSX is very **minimal as a templating languange** because it lets you organize data and logic using JS.

#### The rules of JSX

1. **Return a single root element**</br>
To return multiple elements from a component, wrap them with a **single parent tag**: `div` or `React.Fragment`.</br>
Fragments let you group things without leaving any trace in the browser HTML tree.

2. **Close all the tags**</br>
JSX requires to be explicitly closed: self-closing tags like `<img>` must become `<img/>`.

3. **camelCase most of the things**</br>
**JSX turns into JS and attributes written in JSX become keys of JS objects.**

For historical reasons, `aria-*` and `data-*` attributes are written as in HTML with dashes.

### JS in JSX with Curly Braces
JSX lets you write HTML-like markup inside a JS file, keeping **rendering logic and content, styles in the same place**.

**Any JS expression will work between curly braces, including functions calls.**

The next time you see {{ and }} in JSX, you know thats nothing more than an object inside the JSX curlies.

### Passing Props to a Component
React components use **props** to communicate with each other. Every parent component **can pass some information to is child components by givin them props**.

Props are the information you pass to a JSX tag. 

**Props let you think about parent and child components independently**.

#### Forwarding props with the JSX spread syntax
There is nothing wrong with repetetive code - it can be more legible/readable. But at times you may value conciseness. **Some components forward all of their props to their children. Like how this `Profile` does with `Avatar`**.

```tsx
function Profile(props) {
  return (
    <div className="card">
      <Avatar {...props} />
    </div>
  );
}
```
**Use spread syntax with restraint.** If you are using it in every other component, something is wrong. Often, it indicates that **you should split your components and pass children as JSX.** 









































