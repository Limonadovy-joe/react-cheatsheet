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
    - [Booleans, Null, and Undefined Are Ignored](#booleans-null-and-undefined-are-ignored)
  - [JS in JSX with Curly Braces](#js-in-jsx-with-curly-braces)
  - [Passing Props to a Component](#passing-props-to-a-component)
    - [Forwarding props with the JSX spread syntax](#forwarding-props-with-the-jsx-spread-syntax)
    - [Passing JSX as children](#passing-jsx-as-children)
    - [Props vs State](#props-vs-state)
  - [Conditional rendering](#conditional-rendering)
    - [Conditionally returning nothing with null](#conditionally-returning-nothing-with-null)
    - [Conditionally including JSX](#conditionally-including-jsx)
  - [Rendering Lists](#rendering-lists)
    - [Keeping list items in order with key](#keeping-list-items-in-order-with-key)
    - [Displaying several DOM nodes for each list item](#displaying-several-DOM-nodes-for-each-list-item)
    - [Where to get your keys](#where-to-get-your-keys)
    - [Rules of keys](#rules-of-keys)
    - [Why does React need keys?](#why-does-React-need-keys)
  - [Keeping components pure](#keeping-components-pure)
    - [Side Effects](#side-effects)
    - [Local mutation](#local-mutation)
    - [Where you can cause side effects](#where-you-can-cause-side-effects)
    - [Why does React care about purity?](#why-does-React-care-about-purity)
  - [Your UI as a Tree](#your-ui-as-a-tree)
    - [The Render Tree](#the-render-tree)
    - [The module dependency tree](#the-module-dependency-tree)
- [Adding interactivity](#adding-interactivity)
  - [Responding to Events](#responding-to-events)
    - [Event propagation](#event-propagation)
    - [Stopping propagation](#stopping-propagation])
    - [Naming event handler props](#naming-event-handler-props)
    - [Capture phase events](#capture-phase-events)
    - [Passing handlers as alternative to propagation](#passing-handlers-as-alternative-to-propagation)
    - [Preventing default behavior](#preventing-default-behavior)
  - [State components memory](#state-components-memory)
    - [How does React know which state to return](#how-does-react-know-which-state-to-return)
  - [Render and Commit](#render-and-commit)
  - [State as a snapshot](#state-as-a-snapshot)
    - [Setting state triggers renders](#setting-state-triggers-renders)
  - [Queueing a Series of State Updates](#queueing-a-series-of-state-updates)
    - [React batches state updates](#react-batches-state-updates)
    - [What happens if you update state after replacing it](#what-happens-if-you-update-state-after-replacing-it)
    - [What happens if you replace state after updating it](#what-happens-if-you-replace-state-after-updating-it)
  - [Updating objects in State](#updating-objects-in-state)
    - [Updating a nested object](#updating-a-nested-object)
    - [Write concise update logic with Immer](#write-concise-update-logic-with-immer)
    - [Why is mutating state not recommended in React](#why-is-mutating-state-not-recommended-in-react)
  - [Updating arrays in State](#updating-arrays-in-state)
    - [Adding to an array](#adding-to-an-array)
    - [Replacing items in an array ](#replacing-items-in-an-array)
- [Managing State](#managing-state)
  - [Reacting to input with state](#reacting-to-input-with-state)
  - [Choosing the State Structure](#choosing-the-state-structure)
  - [Sharing State Between Components](#sharing-state-between-components)
  - [Preserving and Resetting State](#preserving-and-resetting-state)
  - [Extracting State Logic into a Reducer](#extracting-state-logic-into-a-reducer)
  - [Passing Data Deeply with Context](#passing-data-deeply-with-context)
  - [Scaling Up with Reducer and Context](#scaling-up-with-reducer-and-context)
- [Escape Hatches](#escape-hatches)
  - [Referencing values with refs](#referencing-values-with-refs)
  - [Manipulating the DOM with Refs](#manipulating-the-dom-with-refs)
  - [Synchronizing with Effects](#synchronizing-with-effects)
- [Anti patterns](#anti-patterns)
  - [Conditional rendering using short circuit operators](#conditional-rendering-using-short-circuit-operators)
- [Best practises](#best-practises)
  - [Organize helper functions](#organize-helper-functions)

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

#### Booleans, Null, and Undefined Are Ignored
false, null, undefined, and true are valid children. **They simply don’t render**. These JSX expressions will all render to the same thing:
```tsx
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>
```

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

#### Passing JSX as children
Sometimes you will want to nest your components. You will see this flexible pattern in many cases.
```tsx
<Card>
  <Avatar />
</Card>
```
You will often use the `children` prop for visual wrappers:
1. **Layout components: `<Container/>, <Row/>, <Column/>, <Grid/>`**
2. **Modal components - components that create modal dialogs or pop-up windows:**
```tsx

type User = {
  fullName: string;
  age: number;
  email: string;
};

type ButtonMouseEventHandler = (evt: MouseEvent<HTMLButtonElement>) => void;

type ModalProps = {
  children?: ReactNode;
  isOpen: boolean;
  onClose: ButtonMouseEventHandler;
};

const Modal = ({ children, isOpen, onClose }: ModalProps) => {
  return !isOpen ? null : (
    <div className="modal">
      <div className="modal-content">{children}</div>
      <button className="modal-close-button" onClick={onClose}></button>
    </div>
  );
};

type UserInfoProps = { user: User };

const UserInfo = ({ user }: UserInfoProps) => (
  <ul>
    {Object.keys(user).map((prop) => (
      <li key={prop}>
        `${prop}: ${user[prop as keyof User]}`
      </li>
    ))}
  </ul>
);

const UserProfileContainer = () => {
  const [isUserModalOpen, setIsUserModalOpen] = useState<boolean>(false);
  const [user] = useState<User>({
    age: 12,
    email: "s@gmail.com",
    fullName: "tom tom",
  });

  const handleModalClose: ButtonMouseEventHandler = (evt) =>
    setIsUserModalOpen((val) => !val);

  return (
    <div className="user-info">
      <h3>User Info:</h3>
      <Modal isOpen={isUserModalOpen} onClose={handleModalClose}>
        <UserInfo user={user} />
      </Modal>
    </div>
  );
};
```
3. **Higher-order components - HOcs - are functions that accept a component as an argument and return a new component with additional props or behavior. They typically use `children` prop to wrap the input component:**
```tsx
TODO
```
4. **Wrapper components**
5. **Render Props components** - components that **accept a function as child** and pass data or behavior to that function.

#### Recap
- Props reflects a componets data at any point in time
- Props are **immutable**
- when a component needs to change its props, **it will have to ask its parrent component to pass it different props**
- props are **read-only snapshots** in time: every render recevies a new version of props
- both **props** and **state** changes trigger a render update

<br>

- it is a means of transmitting data
- **props only work one-way from top to bottom**, however it can receive a callback from parent component through props a then calls it. 
This **one-way** approach greatly reduces the complexity of applications.
- `render()` is called when **state or props change** and also its **entire subtree of descendants is re-rendered**
- all data flows downwards

#### Props vs State
- the **state of one component often become the props of a child component**
- What if the child needs to change its `name` prop? This is usually done through **parent callbacks** or **child events**. The parent would then **subscribe to the event by passing a callback handler.**
```tsx
<Person name={name} onNameChanged={handleNameChange}/>
```
- since the prop is owned by the parent, only the parent should change it


### Conditional rendering
In React you can create **distinct components that encapsulate behaviour you need**. Then you can render **only some of them, depending on the state of your application.**

#### Conditionally returning nothing with `null`
In some situations, you wont want to render anything at all. 
```tsx
function Item({ name, isPacked }) {
  if (isPacked) {
    return null;
  }
  return <li className="item">{name}</li>;
}
```
If `isPacked` is true, the component will return `null`, nothing. Otherwise, it will return JSX to render. <br>
In practise, returning `null` from a component isn`t common because it might suprise a developer trying to render it. More often, you would **conditionally include JSX.**

#### Conditionally including JSX
You may have already notice some duplication in the render output:
```tsx
<li className="item">{name} ✔</li>
```
is very similar to
```tsx
<li className="item">{name}</li>
```
Both of the conditional branches return:
```tsx
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```
While this duplication isnt harmful, it could make your code  **harder to maintain**.</br>
**What if you want to change the `className`?**
You would have to do it in two places in your code. In such situation, you could **conditionally include a little JSX** to make your code more **DRY**.

```tsx
return (
  <li className="item">
    {isPacked ? name + ' ✔' : name}
  </li>
);
```
**If your components get messy with too much nested conditional markup, consider extracting child components to clean up things.**

### Rendering Lists
#### Keeping list items in order with key
**Keys tell React which array item corresponds to each component**, so that it can match them up later. This becomse important if your **array items can move due to sorting, get inserted, or get deleted.** 

Rather then generating keys **on the fly**, you should include them in your **data model**.

#### Displaying several DOM nodes for each list item
```tsx
import { Fragment } from 'react';

const listItems = people.map(person =>
  <Fragment key={person.id}>
    <h1>{person.name}</h1>
    <p>{person.bio}</p>
  </Fragment>
);
```
Fragments disappear from the DOM, so this will produce a flat list.

#### Where to get your keys
Different sources of data provide different sources of keys:
- **Data from database**: If your data come from database, you can use the database keys/IDs, which are unique by nature
- **Locally generated data**: If your data is generated and persited locally - use `Crypto Web Api` or `UUID` lib

#### Rules of keys
- **Keys must be unique among siblings**. However, its okay to use **the same keys for JSX nodes in different arrays**
- **Keys must not change** or that defeats their purpose. Dont generate them while rendering.


#### Why does React need keys?
They let us **uniquely identify an item between its siblings between re-renders**. Even if the position changes **due to reordering**, the `key` lets React identify the item throughout its lifetime.


## Keeping components pure
### Side effects
Reacts rendering process must always be pure. 

**Detecting impure calculations with StrictMode**: In React there are three kinds of inputs that you can read while rendering **`props`, `state` or `context`**.
You should always treat these inputs as **readonly**.

### Local mutation
**`Mutation`** - does mutate variables outside of the function scope or objects that were created before the call - that makes them impure.

However, **its completely fine to change variables and objects that you have just created while rendering.** 
```tsx
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  let cups = [];
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />);
  }
  return cups;
}
```
 This is called **“local mutation”**.

 ### Where you can cause side effects
 Changes like - updating the screen, starting an animation, changing the data are called **side effects.** 

 In React, **side effects usually belong inside event handlers.** Event handlers **dont run during rendering.** So event handlers **dont need to be pure.**

 ### Why does React care about purity?
 - Your components could **run in different enviroments** - on the server. (Since they return the same result for the same inputs)
 - You can improve performance by **skipping rendering components** whose inputs have not changed. This is safe because pure functions always return the same results, so they are safe to cache.


 ## Your UI as a Tree
 Browsers use **tree structures** to model **HTML - DOM** and **CSS - CSSOM**. Mobile platforms also use trees to represent their **view hierarchy**.

### The Render Tree 
When we render A React app, we can model this relationship in a tree, know as the **render tree.**</br>
**The CSSOM and DOM trees are combined into a render tree**, which is then used to compute the **layout of each visible element** and **serves as an input to the paint process that renders the pixels to the screen**.

React as a UI framework, **is platform agnostic**. But A React app could just as likely **render to a mobile or desktop platform**, **which may use different UI primitives like UIView or FrameworkElement.**

These trees are generally helpful for **identifying what the top-level and leaf components are in React app**: 
- **Top-level components** - are the components **nearest to the root component** and affect the rendering performance of all the components benath them and **often contain the most complexity**.
- **Leaf components** - are **components near the bottom of tree** and **have no child components and are often frequently re-rendered**

### The module dependency tree
**Dependency tree** for short.

**Each node** in a module dependency tree is a **module** and **each branch** represents an **`import` statement** in that module.

## Adding interactivity
In React, data that changes over time is called **state**. State is a **components specific memory**. 

Components often need to change whats on the screen as a result of interaction.

### Responding to Events
By convention, it is common to name event handlers as `handle` followed by the element that triggered the event and followed by event name: `handleMouseEnter` || `handleButtonHover`.

If you use **design system**, its common for components like **buttons to contain styling but not specify behavior**. Instead, components like `PlayButton` and 
`UploadButton` **will pass event handlers down**.

### Naming event handler props
By convention, event handler props should start with `on`, followed by a capital letter - `onClick`.

When your component supports multiple interactions, you might name event handler props from app-specific concepts - `onPlayMovie` || `onUploadImage`.

Notice how the `App`` component does not need to know what `Toolbar` component will do with `onPlayMovie` or `onUploadImage`. Thats an **implementation detail**
of `Toolbar`. Here, `Toolbar` passes them down as `onClick` handlers to its `Button`, **but it could later trigger them on keyboard shortcut**.

Naming props after **app-specific interactions like `onPlayMovie`  gives you the flexibility to change how they are used later**.

```tsx
export default function App() {
  return (
    <Toolbar
      onPlayMovie={() => alert('Playing!')}
      onUploadImage={() => alert('Uploading!')}
    />
  );
}

function Toolbar({ onPlayMovie, onUploadImage }) {
  return (
    <div>
      <Button onClick={onPlayMovie}>
        Play Movie
      </Button>
      <Button onClick={onUploadImage}>
        Upload Image
      </Button>
    </div>
  );
}

function Button({ onClick, children }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

### Event propagation
Event handlers will also **catch events from any children your component might have**. Event **bubbles** or **propagetes** up the tree.

### Stopping propagation
Event object also lets you stop the propagation. Calls `e.stopPropagation()`, **preventing the event from bubbling further**.

### Capture phase events
In rare cases, you might need to catch all events on child elems, **even if they stopped propagation**. 

For example, you want to log every click to analytics, regardless of the propagation logic.
```tsx
<div onClickCapture={() => { /* this runs first */ }}>
  <button onClick={e => e.stopPropagation()} />
  <button onClick={e => e.stopPropagation()} />
</div>
```

### Passing handlers as alternative to propagation
```tsx
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}
```
It lets the child component handle the event, while also letting the parent component specify some additional behavior. **If you rely on propagation and its difficult to trace which handlers execute and why, try this approach instead**.

### Preventing default behavior 
Some browser events have default behavior associated with them. You can call `e.preventDefault()` on the event object to stop this from happening.

## State components memory
State is isolated and private

**What if you wanted both componets to keep their states in sync?** Remove state from child components and **add it to their closest shared parent.** 

### How does React know which state to return?
**Hooks rely on a stable call order on every render of the same component.**

Internally, React holds an **array of state pairs for every component**. It also maintains the current pair index, which is set to 0 before rendering. Each time you call useState, **React gives you the next state pair and increments the index.**

## Render and Commit
Process of serving UI has three steps:
1. **Triggering** a render
  - There are two reasons for a component to render:
    1. Component`s **initial render**
    2. The component`s state **has been updated**
2. **Rendering** a component
  - After you trigger a render,React calls your componets to figure out what to display on screen,
    **Rendering** is React calling your components. This process is **recursive**.
  -  **During the initial render**, React **will create DOM nodes** for markup
  -  **During a re-render**, React will calculate **which of their properties**, if any, **have changed since the previous render.**
      It wont do anything with that information until the next step, **the commit phase**.
3. **Commiting** to a DOM
  - React commits changes to the DOM
  - After rendering your components, **React will modify the DOM**.
  - For the **initial render**, React will use the `appendChild()` DOM API to put all the DOM nodes it has created on the screen
  - For **re-render**, React will apply the minimal necessary operatins
  - **React only changes the DOM nodes if there is a difference between re-renders.**

## State as a snapshot
State behaves more like **a snapshot.**

### Setting state triggers renders 
- For an UI to react to the event, you need to update the state
- The JSX you return from the function is **like a snapshot of the UI in time**
- When React calls your component, it **gives you a snapshot of the state for that particual render**
- **Setting state only changes it for the next render!**
- **State variable`s value never changes within a render**, even if its event handler`s code is asynchronous.
- **React keeps the state values “fixed” within one render’s event handlers.** You dont need to worry about whether the state has changed while the code
  is running.
- if you want to read the **latest state before re-render**, you will want to use a **state updater function**

## Queueing a Series of State Updates
Sometimes you want to perform multiple operations on the value queueing the next render.

### React batches state updates
The UI wont be updated untill after your event handler, and any code in it, completes.

React processes **state updates after event handlers have finished running.** This is called batching.

```tsx
export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}
```
But there is one other factor at play here. **React waits until all code in the event handlers has run before processing your state updated.** This is why the re-render only happens after all these `setNumber()` calls.

This lets you update multiple state variables, even from multiple components, without triggering too many **re-renders.**

### Updating the same state multiple times before the next render 
You can pass a function that calculates the next state based on the previous one in the queue, like `setNumber(n => n + 1)`. It is a way to tell React to “do something with the state value”.

### What happens if you update state after replacing it
```tsx
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
}}>
```
React stores 6 as the final result and returns it from useState.

### What happens if you replace state after updating it 
```tsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
        setNumber(42);
      }}>Increase the number</button>
    </>
  )
}
```

React stores 42 as the final result and returns it from useState.


## Updating objects in State
When you want to update an object, you need to create the new one and then set the state to use that copy.

- **Treat state as read-only**
- Mutation is only a problem when you change existing objects that are already in state.

### Updating a nested object
```tsx
const [person, setPerson] = useState({
  name: 'Niki de Saint Phalle',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg',
    image: 'https://i.imgur.com/Sd1AgUOm.jpg',
  }
});

setPerson({
  ...person, // Copy other fields
  artwork: { // but replace the artwork
    ...person.artwork, // with the same one
    city: 'New Delhi' // but in New Delhi!
  }
});
```

### Write concise update logic with Immer
To reduce repetitive copying code, use Immer.

The draft provided by Immer is a special type of object, called a Proxy, that “records” what you do with it. This is why you can mutate it freely as much as you like!

### Why is mutating state not recommended in React
- **Debugging**: If you use console.log and don’t mutate state, your past logs won’t get clobbered by the more recent state changes. So you can clearly see how state has changed between renders.
- **Optimizations**: Common React optimization strategies rely on **skipping work if previous props or state are the same as the next ones**. If you never mutate state, it is very fast to check whether there were any changes. If prevObj === obj, you can be sure that nothing could have changed inside of it.
- **Requirement Changes**: Some application features, like implementing Undo/Redo, showing a history of changes, or letting the user reset a form to earlier values, are easier to do when nothing is mutated. This is because you can keep past copies of state in memory, and reuse them when appropriate. If you start with a mutative approach, features like this can be difficult to add later on.

## Updating arrays in State
You should treat arrays in React state as **read-only.**

### Adding to an array 

```tsx
setArtists([
  { id: nextId++, name: name },
  ...artists // Put old items at the end
]);
```
In this way, spread can do the job of both push() by adding to the end of an array and unshift() by adding to the beginning of an array.

### Replacing items in an array
It is particularly common to want to replace one or more items in an array.

```tsx

let initialCounters = [
  0, 0, 0
];

export default function CounterList() {
  const [counters, setCounters] = useState(
    initialCounters
  );

  function handleIncrementClick(index) {
    const nextCounters = counters.map((c, i) => {
      if (i === index) {
        // Increment the clicked counter
        return c + 1;
      } else {
        // The rest haven't changed
        return c;
      }
    });
    setCounters(nextCounters);
  }

  return (
    <ul>
      {counters.map((counter, i) => (
        <li key={i}>
          {counter}
          <button onClick={() => {
            handleIncrementClick(i);
          }}>+1</button>
        </li>
      ))}
    </ul>
  );
}
```

# Managing State


## Reacting to input with state
React provides **a declarative way to manipulate the UI.** Instead of manipulating **individual pieces of the UI directly**, you describe the different states that your component can be in, and switch between them in response to the user input.

Thinking about UI declaratively.

Expressing **all interactions as state changes** lets you later introduce new visual states without breaking existing ones.  It also lets you change what should be displayed in each state without changing the logic of the interaction itself.

## Choosing the State Structure

**Principles for structuring state:**
1. **Group related state.** If you always update two or more state variables at the same time, consider merging them into a single state variable.
2. **Avoid contradictions in state.** When the state is structured in a way that several pieces of state may contradict and “disagree” with each other, you leave room for mistakes. Try to avoid this. **Making Illegal States Unrepresentable**
3. **Avoid redundant state**. If you can calculate some information from the component’s props or its existing state variables during rendering, you should not put that information into that component’s state.

**Don’t mirror props in state**

```tsx
function Message({ messageColor }) {
  const [color, setColor] = useState(messageColor);
```
Here, a color state variable is initialized to the messageColor prop. **The problem is that if the parent component passes a different value of messageColor later (for example, 'red' instead of 'blue'), the color state variable would not be updated!** The state is only initialized during the first render.
  
4. **Avoid duplication in state**. When the same data is duplicated between multiple state variables, or within nested objects, it is difficult to keep them in sync. Reduce duplication when you can.
5. **Avoid deeply nested state**. Deeply hierarchical state is not very convenient to update. When possible, prefer to structure state in a flat way.
**If the state is too nested to update easily, consider making it “flat”.**
Before normalization:
```tsx
{
  "id": "123",
  "author": {
    "id": "1",
    "name": "Paul"
  },
  "title": "My awesome blog post",
  "comments": [
    {
      "id": "324",
      "commenter": {
        "id": "2",
        "name": "Nicole"
      }
    }
  ]
}
```
After normalization:

```tsx
{
  result: "123",
  entities: {
    "articles": {
      "123": {
        id: "123",
        author: "1",
        title: "My awesome blog post",
        comments: [ "324" ]
      }
    },
    "users": {
      "1": { "id": "1", "name": "Paul" },
      "2": { "id": "2", "name": "Nicole" }
    },
    "comments": {
      "324": { id: "324", "commenter": "2" }
    }
  }
}
```
Sometimes, you can also reduce state nesting **by moving some of the nested state into the child components.** This works well for ephemeral UI state that doesn’t need to be stored, **like whether an item is hovered.**

For UI patterns like selection, keep ID or index in state instead of the object itself.

## Sharing State Between Components

**lifting state up** - Sometimes, you want the state of two components to always change together. To do it, remove state from both of them, move it to their **closest common parent**, and then pass it down to them via props.


1. Remove state from the child components.
2. Pass hardcoded data from the common parent.
3. Add state to the common parent and pass it down together with the event handlers.

**Lifting state up often changes the nature of what you’re storing as state:**
```tsx
export default function Accordion() {
  const [activeIndex, setActiveIndex] = useState(0);
  return (
    <>
      <h2>Almaty, Kazakhstan</h2>
      <Panel
        title="About"
        isActive={activeIndex === 0}
        onShow={() => setActiveIndex(0)}
      >
        With a population of about 2 million, Almaty is Kazakhstan's largest city. From 1929 to 1997, it was its capital city.
      </Panel>
      <Panel
        title="Etymology"
        isActive={activeIndex === 1}
        onShow={() => setActiveIndex(1)}
      >
        The name comes from <span lang="kk-KZ">алма</span>, the Kazakh word for "apple" and is often translated as "full of apples". In fact, the region surrounding Almaty is thought to be the ancestral home of the apple, and the wild <i lang="la">Malus sieversii</i> is considered a likely candidate for the ancestor of the modern domestic apple.
      </Panel>
    </>
  );
}
```

Controlled and uncontrolled components:
It is common to call a component **with some local state “uncontrolled”.** 
For example, the original Panel component with an isActive state variable is uncontrolled because its **parent cannot influence whether the panel is active or not.**

**In contrast, you might say a component is “controlled” when the important information in it is driven by props rather than its own local state. This lets the parent component fully specify its behavior.**


## Preserving and Resetting State
React keeps track of which state belongs to which component based on their place in the UI tree.
If it gets removed, or a different component gets rendered at the same position, React discards its state.

**State is tied to a position in the render tree.**
**React preserves a component’s state for as long as it’s being rendered at its position in the UI tree.**

**Same component at the same position preserves state.**
```tsx

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  if (isFancy) {
    return (
      <div>
        <Counter isFancy={true} />
        <label>
          <input
            type="checkbox"
            checked={isFancy}
            onChange={e => {
              setIsFancy(e.target.checked)
            }}
          />
          Use fancy styling
        </label>
      </div>
    );
  }
  return (
    <div>
      <Counter isFancy={false} />
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}
```

**Different components at the same position reset state:**
Also, when you render a different component in the same position, **it resets the state of its entire subtree.** To see how this works, increment the counter and then tick the checkbox:

```tsx
export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <div>
          <Counter isFancy={true} /> 
        </div>
      ) : (
        <section>
          <Counter isFancy={false} />
        </section>
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}
```

**Resetting state at the same position:**
Option 1: Rendering a component in different positions 

```tsx
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA &&
        <Counter person="Taylor" />
      }
      {!isPlayerA &&
        <Counter person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

Option 2: Resetting state with a key
You can use keys to make React distinguish between any components.

```tsx
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

**Resetting a form with a key:**

```tsx
export default function Messenger() {
  const [to, setTo] = useState(contacts[0]);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedContact={to}
        onSelect={contact => setTo(contact)}
      />
      <Chat key={to.id} contact={to} />
    </div>
  )
}
```

**Preserving state for removed components:**
In a real chat app, you’d probably want to recover the input state when the user selects the previous recipient again. There are a few ways to keep the state “alive” for a component that’s no longer visible:

- You could render all chats instead of just the current one, but hide all the others with CSS. The chats would not get removed from the tree, so their local state would be preserved. This solution works great for simple UIs. **But it can get very slow if the hidden trees are large and contain a lot of DOM nodes.**
- You could **lift the state up and hold the pending message for each recipient in the parent component.** This way, when the child components get removed, it doesn’t matter, because it’s the parent that keeps the important information. This is the most common solution.
- You might also use a different source in addition to React state. For example, you probably want a message draft to persist even if the user accidentally closes the page. To implement this, you could have the Chat component initialize its state by reading from the **localStorage**, and save the drafts there too.

**Fix misplaced state in the list:**
Fix it so that the expanded state is associated with each contact, regardless of the chosen ordering.
From:
```tsx
<ul>
        {displayedContacts.map((contact, i) =>
          <li key={i}>
            <Contact contact={contact} />
          </li>
        )}
      </ul>
```
However, **you want the state to be associated with each particular contact.**
Using the contact ID as a key instead fixes the issue:
```tsx
 <ul>
        {displayedContacts.map(contact =>
          <li key={contact.id}>
            <Contact contact={contact} />
          </li>
        )}
      </ul>
```

## Extracting State Logic into a Reducer
Components with many state updates **spread across many event handlers** can get overwhelming. For these cases, you can **consolidate all the state update logic outside your component in a single function**, called a reducer.

Component logic can be easier to read when you **separate concerns like this.** Now the event handlers only specify **what happened by dispatching actions**, and the reducer function determines **how the state updates in response to them.**

As your application grows in size, you’ll most likely deal with **more complex state transitions**, at which point you’ll be better off using useReducer. 
useReducer provides more **predictable state transitions than useState**, which becomes more important when state changes become so complex that you want to have one place to manage state, like the render function.

useReducer is the better option when you move past managing primitive data, i.e., a string, integer, or Boolean, and **instead must manage a complex object, like with arrays and additional primitives.**

useReducer can help cut down on the code if **many event handlers modify state in a similar way.**

We recommend using a reducer if you often encounter bugs due to incorrect state updates in some component, and want to introduce more structure to its code.

Reducers must be pure. **They should not send requests, schedule timeouts, or perform any side effects - middlewares** (operations that impact things outside the component).

**Each action describes a single user interaction.**

## Passing Data Deeply with Context
Usually, you will pass information from a **parent component to a child component via props.** But passing props can become verbose and inconvenient if you have to pass them **through many components in the middle,** or if many components in your app need the same information. Context lets the parent component make some information available to any component in the tree below it.

How context works might remind you of CSS property inheritance. In CSS, you can specify color: blue for a <div>, and any DOM node inside of it, no matter how deep, will inherit that color unless some other DOM node in the middle overrides it with color: green. Similarly, in React, the only way to override some context coming from above is to wrap children into a context provider with a different value.

Each context that you make with createContext() is completely separate from other ones, and ties together components using and providing that particular context. One component may use or provide many different contexts without a problem.

**Before you use context:**
1. Start by passing props. If your components are not trivial, it’s not unusual to pass a dozen props down through a dozen components. It may feel like a slog, **but it makes it very clear which components use which data!** The person maintaining your code will be glad **you’ve made the data flow explicit with props.**
2. **Extract components and pass JSX as children to them.** If you pass some data through many layers of intermediate components that don’t use that data (and only pass it further down), this often means that you forgot to extract some components along the way. For example, maybe you pass data props like posts to visual components that don’t use them directly, like

```tsx
<Layout posts={posts} />.
```
 Instead, make Layout take children as a prop, and render
 
 ```tsx
<Layout><Posts posts={posts} /></Layout>.
```
**This reduces the number of layers between the component specifying the data and the one that needs it.**

**Use cases for context:**
-  **Theming**: If your app lets the user change its appearance (e.g. dark mode), you can put a context provider at the top of your app, and use that context in components that need to adjust their visual look.
-  **Current account**: Many components might need to know the currently logged in user. Putting it in context makes it convenient to read it anywhere in the tree. Some apps also let you operate multiple accounts at the same time (e.g. to leave a comment as a different user). In those cases, it can be convenient to wrap a part of the UI into a nested provider with a different current account value.
-  **Routing**: Most routing solutions use context internally to hold the current route. This is how every link “knows” whether it’s active or not. If you build your own router, you might want to do it too.
-  **Managing state**: As your app grows, you might end up with a lot of state closer to the top of your app. Many distant components below may want to change it. It is common to use a reducer together with context to manage complex state and pass it down to distant components without too much hassle.

## Scaling Up with Reducer and Context
Reducers let you consolidate a component’s state update logic. Context lets you pass information deep down to other components.

**A reducer helps keep the event handlers short and concise.**

**Currently, the tasks state and the dispatch function are only available in the top-level TaskApp component.** To let other components read the list of tasks or change it, you have to explicitly **pass down the current state and the event handlers that change it as props.**

```tsx
<TaskList
  tasks={tasks}
  onChangeTask={handleChangeTask}
  onDeleteTask={handleDeleteTask}
/>
```

And TaskList passes the event handlers to Task:

```tsx
<Task
  task={task}
  onChange={onChangeTask}
  onDelete={onDeleteTask}
/>
```
In a small example like this, this works well, but if you have tens or hundreds of components in the middle, passing down all state and functions can be quite frustrating!

This is why, as an alternative to passing them through props, you might want to put both **the tasks state and the dispatch function into context.** This way, any component below TaskApp in the tree can read the tasks and dispatch actions without the repetitive “prop drilling”.

```tsx
<TasksContext.Provider value={tasks}>
  <TasksDispatchContext.Provider value={dispatch}>
    <h1>Day off in Kyoto</h1>
    <AddTask />
    <TaskList />
  </TasksDispatchContext.Provider>
</TasksContext.Provider>
```
The TaskApp component does not pass any event handlers down, and the TaskList does not pass any event handlers to the Task component either. Each component reads the context that it needs:

```tsx
export default function TaskList() {
  const tasks = useContext(TasksContext);
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}
```
As your app grows, you may have many **context-reducer pairs** like this. This is a powerful way to scale your app and **lift state up** without too much work whenever you want to access the data deep in the tree.

TasksContext.js
```tsx

const TasksContext = createContext(null);

const TasksDispatchContext = createContext(null);

export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

# Escape Hatches
Some of your components may need to control and synchronize with systems outside of React. For example, you might need to **focus an input using the browser API, play and pause a video player implemented without React**, or connect and listen to messages from a remote server.

Most of your **application logic and data flow** should not rely on these features.

## Referencing values with refs 
When you want a component to “remember” some information, but **you don’t want that information to trigger new renders, you can use a ref.**

Unlike state, ref is a plain JavaScript object with the current property that you can read and modify.

When a piece of information **is used for rendering, keep it in state.** When a piece of information is only needed by event handlers and changing it doesn’t require a re-render, using a ref may be more efficient.

**How does useRef work inside?**

```tsx
// Inside of React
function useRef(initialValue) {
  const [ref, unused] = useState({ current: initialValue });
  return ref;
}
```
React provides a built-in version of useRef because it is common enough in practice. But you can think of it as a regular state variable without a setter. If you’re familiar with object-oriented programming, **refs might remind you of instance fields—but instead of this.something you write somethingRef.current.**


**When to use refs:**
Typically, you will use a ref when your component needs to “step outside” React and communicate with external APIs—often a browser API that won’t impact the appearance of the component.

- **Storing timeout IDs**
- **Storing and manipulating DOM elements**
- Storing other objects that aren’t necessary to calculate the JSX.

**Refs and the DOM:**
However, the most common use case for **a ref is to access a DOM element.** For example, this is handy if you want to **focus an input programmatically.** When you pass a ref to a ref attribute in JSX, like 

```tsx
<div ref={myRef}>
```
React will put the corresponding DOM element into myRef.current. **Once the element is removed from the DOM, React will update myRef.current to be null.**

Don’t read or write ref.current during rendering. This makes your component hard to predict.

Debounced Button
```tsx
function DebouncedButton({ onClick, children }) {
  const timeoutID = useRef(null);
  
  return (
    <button onClick={() => {
      clearTimeout(timeoutID.current);
      console.log(timeoutID.current);
      timeoutID.current = setTimeout(() => {
        onClick();
      }, 1000);
    }}>
      {children}
    </button>
  );
}

export default function Dashboard() {
  return (
    <>
      <DebouncedButton
        onClick={() => alert('Spaceship launched!')}
      >
        Launch the spaceship
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Soup boiled!')}
      >
        Boil the soup
      </DebouncedButton>
      <DebouncedButton
        onClick={() => alert('Lullaby sung!')}
      >
        Sing a lullaby
      </DebouncedButton>
    </>
  )
}
```
**A global variable** like timeoutID is shared between all components. This is why clicking on the second button resets the first button’s pending timeout. 

## Manipulating the DOM with Refs
Sometimes you might need a **access to DOM elemenets managed by React** - for example: to focus a node, scroll to it, or measure its size and position. There is no built-in way to do those things in React,so you will need a **a ref to the DOM node**.


**How to manage a list of refs using a ref callback?**
Sometimes you might need a **ref to each item in the list**, and you don’t know how many you will have. Something like this wouldn’t work:
```tsx
<ul>
  {items.map((item) => {
    // Doesn't work!
    const ref = useRef(null);
    return <li ref={ref} />;
  })}
</ul>
```
This is because Hooks must only be called at the top-level of your component. You can’t call useRef in a loop, in a condition, or inside a map() call.

**Solution is to pass a function to the ref attribute. This is called a ref callback.** React will call your ref callback with the DOM node when it’s time to set the ref, and with null when it’s time to clear it. This lets you maintain your own array or a Map, and access any ref by its index or some kind of ID.

```tsx
export default function CatFriends() {
  const itemsRef = useRef(null);

  function scrollToId(itemId) {
    const map = getMap();
    const node = map.get(itemId);
    node.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center'
    });
  }

  function getMap() {
    if (!itemsRef.current) {
      // Initialize the Map on first usage.
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToId(0)}>
          Tom
        </button>
        <button onClick={() => scrollToId(5)}>
          Maru
        </button>
        <button onClick={() => scrollToId(9)}>
          Jellylorum
        </button>
      </nav>
      <div>
        <ul>
          {catList.map(cat => (
            <li
              key={cat.id}
              ref={(node) => {
                const map = getMap();
                if (node) {
                  map.set(cat.id, node);
                } else {
                  map.delete(cat.id);
                }
              }}
            >
              <img
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}
```
In this example, itemsRef doesn’t hold a single DOM node. Instead, it holds a Map from item ID to a DOM node. (Refs can hold any values!).** The ref callback on every list item takes care to update the Map.**


**Accessing another component’s DOM nodes**</br>
**When you put a ref on a built-in component that outputs a browser element like input**, React will set that ref’s current property to the corresponding DOM node (such as the actual <input /> in the browser).

However, if you try to put a ref on your own component, like MyInput, by default you will get null.
**This happens because by default React does not let a component access the DOM nodes of other components.** Not even for its own children! This is intentional. Refs are an escape hatch that should be used sparingly. Manually manipulating another component’s DOM nodes makes your code even more fragile.

**Instead, components that want to expose their DOM nodes have to opt in to that behavior**. A component can specify that it “forwards” its ref to one of its children. Here’s how MyInput can use the forwardRef API:

```tsx
const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});
```
In design systems, **it is a common pattern for low-level components like buttons, inputs, and so on, to forward their refs to their DOM nodes.** On the other hand, high-level components like forms, lists, or page sections usually won’t expose their DOM nodes to avoid accidental dependencies on the DOM structure.

**Exposing a subset of the API with an imperative handle:**
In the above example, MyInput exposes the original DOM input element. This lets the parent component call focus() on it. However, this also lets the parent component do something else—for example, change its CSS styles. In uncommon cases, you may want to restrict the exposed functionality. You can do that with useImperativeHandle:
```tsx
import {
  forwardRef, 
  useRef, 
  useImperativeHandle
} from 'react';

const MyInput = forwardRef((props, ref) => {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    // Only expose focus and nothing else
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input {...props} ref={realInputRef} />;
});

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```
Here, realInputRef inside MyInput holds the actual input DOM node. However, **useImperativeHandle instructs React to provide your own special object as the value of a ref to the parent component.** **So inputRef.current inside the Form component will only have the focus method**. In this case, the ref “handle” is not the DOM node, but the custom object you create inside useImperativeHandle call.

**When React attaches the refs:**
In React, **every update is split in two phases:**
1. During **render**, React calls your components to figure out what should be on the screen.
2. During **commit**, React applies changes to the DOM.

In general, you don’t want to access refs during rendering. That goes for refs holding DOM nodes as well. During the first render, the DOM nodes have not yet been created, so ref.current will be null. And during the rendering of updates, the DOM nodes haven’t been updated yet. So it’s too early to read them.

React sets ref.current during the commit. Before updating the DOM, React sets the affected ref.current values to null. After updating the DOM, React immediately sets them to the corresponding DOM nodes.

**Usually, you will access refs from event handlers.** If you want to do something with a ref, **but there is no particular event to do it in, you might need an Effect. We will discuss effects on the next pages.**

**Flushing state updates synchronously with flushSync:**
Consider code like this, which adds a new todo and scrolls the screen down to the last child of the list. Notice how, for some reason, it always scrolls to the todo that was just before the last added one:

```tsx
import { useState, useRef } from 'react';

export default function TodoList() {
  const listRef = useRef(null);
  const [text, setText] = useState('');
  const [todos, setTodos] = useState(
    initialTodos
  );

  function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    setText('');
    setTodos([ ...todos, newTodo]);
    listRef.current.lastChild.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest'
    });
  }

  return (
    <>
      <button onClick={handleAdd}>
        Add
      </button>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <ul ref={listRef}>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}
```

The issue is with these two lines:
```tsx
setTodos([ ...todos, newTodo]);
listRef.current.lastChild.scrollIntoView();
```
**In React, state updates are queued.** Usually, this is what you want. However, **here it causes a problem because setTodos does not immediately update the DOM.** So the time you scroll the list to its last element, the todo has not yet been added. This is why scrolling always “lags behind” by one item.

To fix this issue, **you can force React to update (“flush”) the DOM synchronously.**
```tsx
flushSync(() => {
  setTodos([ ...todos, newTodo]);
});
listRef.current.lastChild.scrollIntoView();
```

**Best practices for DOM manipulation with refs:**
Refs are an escape hatch. You should only use them when you have to “step outside React”. Common examples of this include managing focus, scroll position, or calling browser APIs that React does not expose.

Avoid changing DOM nodes managed by React. Modifying, adding children to, or removing children from elements that are managed by React can lead to inconsistent visual results or crashes like above.

However, this doesn’t mean that you can’t do it at all. It requires caution. **You can safely modify parts of the DOM that React has no reason to update.** For example, if some div is always empty in the JSX, React won’t have a reason to touch its children list. Therefore, it is safe to manually add or remove elements there.

**Challenge:**
This image carousel has a “Next” button that switches the active image. Make the gallery scroll horizontally to the active image on click. You will want to call scrollIntoView() on the DOM node of the active image..

You can declare a selectedRef, and then pass it conditionally only to the current image:
```tsx
<li ref={index === i ? selectedRef : null}>
```
When index === i, meaning that the image is the selected one, the li will receive the selectedRef. React will make sure that selectedRef.current always points at the correct DOM node.

Note that the flushSync call is necessary to force React to update the DOM before the scroll. Otherwise, selectedRef.current would always point at the previously selected item.

```tsx
export default function CatFriends() {
  const selectedRef = useRef(null);
  const [index, setIndex] = useState(0);

  return (
    <>
      <nav>
        <button onClick={() => {
          flushSync(() => {
            if (index < catList.length - 1) {
              setIndex(index + 1);
            } else {
              setIndex(0);
            }
          });
          selectedRef.current.scrollIntoView({
            behavior: 'smooth',
            block: 'nearest',
            inline: 'center'
          });            
        }}>
          Next
        </button>
      </nav>
      <div>
        <ul>
          {catList.map((cat, i) => (
            <li
              key={cat.id}
              ref={index === i ?
                selectedRef :
                null
              }
            >
              <img
                className={
                  index === i ?
                    'active'
                    : ''
                }
                src={cat.imageUrl}
                alt={'Cat #' + cat.id}
              />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}
```

## Synchronizing with Effects
Some components need to synchronize with **external systems.**  For example, you might want to control a **non-React component based on the React state**, set up a **server connection**, or **send an analytics log when a component appears on the screen.**

- **Effects let you specify side effects that are caused by rendering itself, rather than by a particular event.**
- **Setting up a server connection is an Effect** because it should happen no matter which interaction caused the component to appear. **Effects run at the end of a commit after the screen updates**. This is a good time to synchronize the React components with some external system.
- Every time your component renders, React will update the screen and then run the code inside useEffect. In other words, **useEffect “delays” a piece of code from running until that render is reflected on the screen.**


**Why was the ref omitted from the dependency array?**
```tsx
function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);
  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying]);
```
- **The set functions returned by useState also have stable identity**, so you will often see them omitted from the dependencies too. If the linter lets you omit a dependency without errors, it is safe to do.
- **Omitting always-stable dependencies only works when the linter can “see” that the object is stable.** For example, **if ref was passed from a parent component, you would have to specify it in the dependency array.** However, this is good because you can’t know whether the parent component always passes the same ref, or passes one of several refs conditionally. So your Effect would depend on which ref is passed.

**The cleanup function:** </br>
**The cleanup function should stop or undo whatever the Effect was doing.** The rule of thumb is that the user shouldn’t be able to distinguish between the Effect running once (as in production) and a setup → cleanup → setup sequence (as you’d see in development).

**Most of the Effects you’ll write will fit into one of the common patterns below:**
- **Controlling non-React widgets**
  - Sometimes you need to add UI widgets that aren’t written to React. For example, let’s say you’re adding a map component to your page.
  - Some APIs may not allow you to call them twice in a row. For example, the showModal method of the built-in <dialog> element throws if you call it twice. Implement the cleanup function and make it close the dialog:
```tsx
useEffect(() => {
  const dialog = dialogRef.current;
  dialog.showModal();
  return () => dialog.close();
}, []);
```
- **Triggering animations**
  - If your Effect animates something in, the cleanup function should reset the animation to the initial values
- **Fetching data**
  - If your Effect fetches something, the cleanup function should either abort the fetch or ignore its result:
```tsx
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```

**Not an Effect: Initializing the application:**
```tsx
if (typeof window !== 'undefined') { // Check if we're running in the browser.
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```
This guarantees that such logic only runs once after the browser loads the page.
































































































































 

 
 








## Anti patterns
### Conditional rendering using short circuit operators
In some situations using short circuit operators for conditional rendering **may backfire and you may end up with an unwanted 0 in your UI**. To avoid this default to using ternary operators. The only caveat is that they’re more verbose.

The short-circuit operator reduces the amount of code which is always nice. Ternaries are more verbose but there is no chance to get it wrong. Plus, adding the alternative condition is less of a change.
```tsx
// 👎 Try to avoid short-circuit operators
type User = {
  id: string;
  name: string;
};

type UserListProps = { users: User[] };

const UserList = ({ users }: UserListProps) => {
  return (
    <div className="user-list">
      <ul>
        {users.length &&
          users.map(({ id, name }) => <li id={id}>name: {name}</li>)}
      </ul>
    </div>
  );
};

// 👍 Use a ternary instead
type User = {
  id: string;
  name: string;
};

type UserListProps = { users: User[] };

const UserList = ({ users }: UserListProps) => {
  return (
    <div className="user-list">
      <ul>
        {!users.length
          ? null
          : users.map(({ id, name }) => <li id={id}>name: {name}</li>)}
      </ul>
    </div>
  );
};


```
## Best practises
### Organize helper functions
Helper functions that **dont need to hold a closure over the components should be moved outside**. So ideal place is before the component definition so the file can be readable from top to bottom.

That also reduces the noise in the component and leaves inside only those things that need to be there.

```tsx
// 👎 Avoid nesting functions which don't need to hold a closure.
function Component({ date }) {
  function parseDate(rawDate) {
    ...
  }

  return <div>Date is {parseDate(date)}</div>
}


function parseDate(date) {
  ...
}

// 👍 Place the helper functions after or before the component
function Component({ date }) {
  return <div>Date is {parseDate(date)}</div>
}
```
You want to keep the least number of helper functions inside the definition. **Move as many as possible and pass the values from state as arguments**.

Composing your logic out of pure functions that rely only on inputs makes it easier to track bugs and extends.
```tsx
// 👎 Helper functions shouldn't read from the component's state
export default function Component() {
  const [value, setValue] = useState('')

  function isValid() {
    // ...
  }

  return (
    <>
      <input
        value={value}
        onChange={e => setValue(e.target.value)}
        onBlur={validateInput}
      />
      <button
        onClick={() => {
          if (isValid() {
            // ...
          }
        }}
      >
        Submit
      </button>
    </>
  )
}

// 👍 Extract them and pass only the values they need
export default function Component() {
  const [value, setValue] = useState('')

  return (
    <>
      <input
        value={value}
        onChange={e => setValue(e.target.value)}
        onBlur={validateInput}
      />
      <button
        onClick={() => {
          if (isValid(value)) {
            // ...
          }
        }}
      >
        Submit
      </button>
    </>
  )
}

function isValid(value) {
  // ...
}
```



















































