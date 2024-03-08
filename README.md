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
    - [Stopwatch using ref](#stopwatch-using-ref)
    - [Chat using ref](#chat-using-ref) 
  - [Manipulating the DOM with Refs](#manipulating-the-dom-with-refs)
    - [Managing a list of refs using a ref callback](#managing-a-list-of-refs-using-a-ref-callback)
    - [Ref forwarding](#ref-forwarding) 
  - [Synchronizing with Effects](#synchronizing-with-effects)
  - [You Might Not Need an Effect](#you-might-not-need-an-effect)
  - [Lifecycle of Reactive Effects](#lifecycle-of-reactive-effects)
  - [Separating Events from Effects](#separating-events-from-effects)
  - [Removing Effect Dependencies](#removing-effect-dependencies)
  - [Reusing Logic with Custom Hooks](#reusing-logic-with-custom-hooks)
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
- When you want a component to “remember” some information, but **you don’t want that information to trigger new renders, you can use a ref.**
- Unlike state, ref is a plain JavaScript object with the current property that you can read and modify.
- When a piece of information **is used for rendering, keep it in state.** When a piece of information is only needed by event handlers and changing it doesn’t require a re-render, using a ref may be more efficient.

### Stopwatch using ref
```tsx
import React, { useState, useRef, MutableRefObject } from 'react';
import * as T from 'fp-ts/Task';
import * as IO from 'fp-ts/IO';
import { pipe, constVoid, flow } from 'fp-ts/function';
import * as O from 'fp-ts/Option';

//  TODO
// using class syntax - where intervalId will be located in class state
// using customHook
// Stop button does not work yet
type IntervalId = ReturnType<typeof setInterval>;

const setIntervalPromise =
  <T,>(fun: IO.IO<T>) =>
  (delay: number): T.Task<IntervalId> => {
    return () =>
      new Promise((resolve) => {
        const intervalId = setInterval(() => {
          fun();
        }, delay);
        resolve(intervalId);
      });
  };

const calculateElapsedTime = (startTime: number, endTime: number) => {
  const elapsedTime = endTime - startTime;

  const ms = elapsedTime;
  const sec = ms / 1000;
  const min = sec / 60;
  const hour = min / 60;
  return { ms, sec, min, hour } as const;
};

const mapIntervalIdRef =
  (ref: MutableRefObject<O.Option<IntervalId>>) => (intervalId: IntervalId) =>
    O.Functor.map(ref.current, () => intervalId);

const setIntervalIdRef =
  (ref: MutableRefObject<O.Option<IntervalId>>) =>
  (option: O.Option<IntervalId>): IO.IO<void> =>
  () =>
    (ref.current = option);

const clearIntervalIO =
  (intervalId: IntervalId): IO.IO<void> =>
  () =>
    clearInterval(intervalId);

export const Stopwatch = () => {
  const [startTime, setStartTime] = useState(0);
  const [now, setNow] = useState(0);
  const intervalIdRef = useRef<O.Option<IntervalId>>(O.none);

  const start = () => () => {
    const setStartTimeThunk = () => setStartTime(Date.now());
    setStartTimeThunk();

    const setNowThunk = () => setNow(Date.now());
    setNowThunk();

    const setIntervalAsync = () => {
      const delay = 50;

      return pipe(
        delay,
        setIntervalPromise(setNowThunk),
        T.chain(flow(mapIntervalIdRef(intervalIdRef), setIntervalIdRef(intervalIdRef), T.fromIO))
      );
    };

    setIntervalAsync()();
  };

  const stop = () => () => pipe(intervalIdRef.current, O.fold(constVoid, clearIntervalIO));

  return (
    <>
      <h1>Time passed: {calculateElapsedTime(startTime, now).sec.toFixed(3)}</h1>
      <button onClick={start()}>Start</button>
      <button onClick={stop()}>Stop</button>
    </>
  );
};

```

### Chat using ref
```tsx
import React, { useState, useRef, MutableRefObject } from 'react';

type TimeoutId = ReturnType<typeof setTimeout>;

const setTimeoutPromise = (cb: () => void, delay: number) =>
  new Promise<TimeoutId>((resolve) => {
    const timeoutID = setTimeout(() => {
      cb();
    }, delay);
    resolve(timeoutID);
  });

const setTimeoutIdRef =
  (ref: MutableRefObject<NodeJS.Timeout | undefined>) => (timeoutId: TimeoutId) => {
    ref.current = timeoutId;
  };

export const Chat = () => {
  const [text, setText] = useState('');
  const [isSending, setIsSending] = useState(false);
  const timeoutIdRef = useRef<TimeoutId>();

  const setNotSending = () => setIsSending(false);

  const handleFormSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    setIsSending(true);

    const TIMEOUT_DELAY = 3000;

    setTimeoutPromise(() => {
      alert('sended txt: ' + text);
      setNotSending();
    }, TIMEOUT_DELAY).then(setTimeoutIdRef(timeoutIdRef));
  };

  const handleFormUndo = () => {
    if (timeoutIdRef.current) clearTimeout(timeoutIdRef.current);
    setNotSending();
  };

  return (
    <form action="" onSubmit={handleFormSubmit}>
      <input type="text" value={text} onChange={(e) => setText(e.target.value)} />
      <button type="submit" disabled={isSending}>
        {isSending ? 'Sending...' : 'Send'}
      </button>
      {isSending && <button onClick={handleFormUndo}>Undo</button>}
    </form>
  );
};
```

### Debouncing using ref
```tsx
import React from 'react';

const debounce = (fun: () => void, delay: number) => {
  let timeoutId: null | number = null;

  return () => {
    if (timeoutId !== null) clearTimeout(timeoutId);
    timeoutId = setTimeout(fun, delay) as unknown as number;
  };
};

const throttle = (fun: () => void, delay: number) => {
  let isRunning = false;

  return () => {
    if (!isRunning) {
      isRunning = true;
      setTimeout(() => {
        isRunning = false;
        fun();
      }, delay);
    }
  };
};

type ButtonProps = {
  onClick: () => void;
  children: React.ReactNode;
};

const DebouncedButton = ({ onClick, children }: ButtonProps) => {
  return <button onClick={() => onClick()}>{children}</button>;
};

export const Dashboard = () => {
  const handleFirstButtonClick = debounce(() => alert('Button 1 clicked!'), 500);
  const handleSecondButtonClick = throttle(() => alert('Button 2 clicked!'), 1000);
  const handleThirdButtonClick = debounce(() => alert('Button 3 clicked!'), 500);

  return (
    <>
      <h3>Dashboard</h3>
      <DebouncedButton onClick={handleFirstButtonClick}>Button 1</DebouncedButton>
      <DebouncedButton onClick={handleSecondButtonClick}>Button 2</DebouncedButton>
      <DebouncedButton onClick={handleThirdButtonClick}>Button 3</DebouncedButton>
    </>
  );
};

```


## Manipulating the DOM with Refs
Sometimes you might need a **access to DOM elemenets managed by React** - for example: to focus a node, scroll to it, or measure its size and position. There is no built-in way to do those things in React,so you will need a **a ref to the DOM node**.

### Managing a list of refs using a ref callback
```tsx
import React, { ComponentPropsWithRef, useRef } from 'react';

type Dimensions = {
  width: number;
  height: number;
};

type Image = { src: string; dimensions: Dimensions };
type Images = Array<Image>;

const createImage = (src: string): Image => {
  const IMAGE_DIMENSION = 120;
  return { src, dimensions: { width: IMAGE_DIMENSION, height: IMAGE_DIMENSION } };
};

const createImageDescription = (identifier: number) => `Image - ${identifier + 1}`;

export const createImages = () =>
  [
    'https://placekitten.com/g/200/200',
    'https://placekitten.com/g/300/200',
    'https://placekitten.com/g/250/200',
  ].map(createImage);

const useMap = <K, V>() => {
  const mapRef = useRef(new Map<K, V>());
  const map = mapRef.current;

  const set = (key: K, value: V) => map.set(key, value);
  const get = (key: K) => map.get(key);
  const remove = (key: K) => map.delete(key);

  return { set, get, remove, size: map.size, map };
};

type ImageProps = {
  src: string;
  description: string;
  dimensions: Dimensions;
  onImageRender: (id: string, instance: HTMLImageElement | null) => void;
} & ComponentPropsWithRef<'img'>;

const Image = (props: ImageProps) => {
  const {
    src,
    description,
    dimensions: { width, height },
    onImageRender,
    ...rest
  } = props;

  return (
    <img
      src={src}
      alt={description}
      width={width}
      height={height}
      {...rest}
      ref={(node) => onImageRender(description, node)}
    />
  );
};

type ImagesListProps = {
  images: Images;
  onImageRender: (id: string, instance: HTMLImageElement | null) => void;
};

const ImagesList = (props: ImagesListProps) => {
  const { images, ...rest } = props;

  return (
    <div>
      {images.map(({ src, dimensions }, imageNumber) => {
        const description = createImageDescription(imageNumber);
        return (
          <Image key={src} src={src} description={description} dimensions={dimensions} {...rest} />
        );
      })}
    </div>
  );
};

type ImageGalleryNavProps = {
  images: Images;
  onItemClick: (id: string) => void;
};

const ImageGalleryNav = (props: ImageGalleryNavProps) => {
  const { images, onItemClick } = props;

  return (
    <div>
      {images.map(({ src }, index) => {
        const desc = createImageDescription(index);
        return (
          <button onClick={() => onItemClick(desc)} key={'button' + src}>
            {desc}
          </button>
        );
      })}
    </div>
  );
};

type ImageGalleryProps = {
  images: Images;
};

export const ImageGallery = (props: ImageGalleryProps) => {
  const { images } = props;

  const { get, set, remove } = useMap<string, HTMLImageElement>();

  const handleImageRender = (id: string, instance: HTMLImageElement | null) => {
    instance !== null ? set(id, instance) : remove(id);
  };

  const handleNavItemClick = (id: string) => {
    const node = get(id);
    if (node) node.scrollIntoView({ behavior: 'smooth', block: 'nearest', inline: 'center' });
  };

  return (
    <div>
      <p>Image Gallery</p>
      <ImageGalleryNav images={images} onItemClick={handleNavItemClick} />
      <ImagesList images={images} onImageRender={handleImageRender} />
    </div>
  );
};

```
### Ref forwarding
- **React does not let a component access the DOM nodes of other components**. Not even for its own children! This is intentional. Refs are an escape hatch that should be used sparingly. Manually manipulating another component’s DOM nodes makes your code even more fragile.
- Instead, components that want to expose their DOM nodes have to opt in to that behavior. A component can specify that it “forwards” its ref to one of its children

- In design systems, **it is a common pattern for low-level components like buttons, inputs, and so on, to forward their refs to their DOM nodes.**
- On the other hand, **high-level components like forms, lists, or page sections usually won’t expose their DOM nodes to avoid accidental dependencies on the DOM structure.**

```tsx
import React, {
  forwardRef,
  ComponentPropsWithRef,
  ComponentPropsWithoutRef,
  ReactNode,
  useRef,
} from 'react';

type ButtonProps = {
  children: ReactNode;
  onClick: (() => void) | ((e: React.MouseEvent<HTMLButtonElement, MouseEvent>) => void);
} & ComponentPropsWithoutRef<'button'>;

const Button = ({ children, onClick }: ButtonProps) => (
  <button onClick={onClick}>{children}</button>
);

type InputProps = {
  labelText: string;
} & ComponentPropsWithRef<'input'>;

const Input = forwardRef<HTMLInputElement, InputProps>(({ labelText, value, ...rest }, ref) => (
  <label>
    {labelText}
    <input type="text" ref={ref} value={value} {...rest} />
  </label>
));

Input.displayName = 'Input';

export const Form = () => {
  const inputRef = useRef<HTMLInputElement>(null);

  const handleButtonClick = () => {
    if (inputRef.current !== null) inputRef.current.focus();
  };

  return (
    <form action="" onSubmit={(e) => e.preventDefault()}>
      <Input labelText="Name:" ref={inputRef} />
      <Button onClick={handleButtonClick}>{'Focus input'}</Button>
    </form>
  );
};
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

## You Might Not Need an Effect
Effects are an **escape hatch** from the React paradigm. They let you “step outside” of React and synchronize your components with some external system like a non-React widget, network, or the browser DOM. Removing unnecessary Effects will make your code easier to follow, faster to run, and less error-prone.  

**How to remove unnecessary Effects**:</br>
- **You don’t need Effects to transform data for rendering.**
- **You don’t need Effects to handle user events.** For example, let’s say you want to send an /api/buy POST request and show a notification when the user buys a product. This is why you’ll usually handle **user events** in the corresponding **event handlers.**

**Remove unnecessary Effects: Caching expensive calculations:**
This component computes visibleTodos by taking the todos it receives by props and filtering them according to the filter prop. You might feel tempted to store the result in state and update it from an Effect:

```tsx
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');

  // 🔴 Avoid: redundant state and unnecessary Effect
  const [visibleTodos, setVisibleTodos] = useState([]);
  useEffect(() => {
    setVisibleTodos(getFilteredTodos(todos, filter));
  }, [todos, filter]);
}
```
Like in the earlier example, this is both unnecessary and inefficient. First, remove the state and the Effect:
```tsx
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ This is fine if getFilteredTodos() is not slow.
  const visibleTodos = getFilteredTodos(todos, filter);
  // ...
}
```
Usually, this code is fine! But maybe getFilteredTodos() is slow or you have a lot of todos. In that case you don’t want to recalculate getFilteredTodos() if some unrelated state variable like newTodo has changed.

You can cache (or “memoize”) an expensive calculation by wrapping it in a useMemo Hook:
```tsx
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  const visibleTodos = useMemo(() => {
    // ✅ Does not re-run unless todos or filter change
    return getFilteredTodos(todos, filter);
  }, [todos, filter]);
  // ...
}
```
This tells React that you don’t want the inner function to re-run unless either todos or filter have changed. React will remember the return value of getFilteredTodos() during the initial render. During the next renders, it will check if todos or filter are different. If they’re the same as last time, useMemo will return the last result it has stored. But if they are different, React will call the inner function again (and store its result).

**The function you wrap in useMemo runs during rendering, so this only works for pure calculations.**

useMemo won’t make the first render faster. It only helps you skip unnecessary work on updates.

Keep in mind that your machine is probably faster than your users’ so it’s a good idea to test the performance with an artificial slowdown. For example, Chrome offers a **CPU Throttling option for this.**

Also note that measuring performance in development will not give you the most accurate results. (For example, when **Strict Mode is on, you will see each component render twice rather than once.)** To get the most accurate timings, build your app for production and test it on a device like your users have.


**Remove unnecessary Effects: Resetting all state when a prop changes:**
This ProfilePage component receives a userId prop. The page contains a comment input, and you use a comment state variable to hold its value. One day, you notice a problem: when you navigate from one profile to another, the comment state does not get reset. As a result, it’s easy to accidentally post a comment on a wrong user’s profile. To fix the issue, you want to clear out the comment state variable whenever the userId changes:
```tsx
export default function ProfilePage({ userId }) {
  const [comment, setComment] = useState('');

  // 🔴 Avoid: Resetting state on prop change in an Effect
  useEffect(() => {
    setComment('');
  }, [userId]);
  // ...
}
```
This is inefficient because ProfilePage and its children will first render with the stale value, and then render again. It is also complicated because you’d need to do this in every component that has some state inside ProfilePage. For example, if the comment UI is nested, you’d want to clear out nested comment state too.

Instead, you can tell React that each user’s profile is conceptually a different profile by giving it an explicit key. Split your component in two and pass a key attribute from the outer component to the inner one:
```tsx
export default function ProfilePage({ userId }) {
  return (
    <Profile
      userId={userId}
      key={userId}
    />
  );
}

function Profile({ userId }) {
  // ✅ This and any other state below will reset on key change automatically
  const [comment, setComment] = useState('');
  // ...
}
```
Normally, React preserves the state when the same component is rendered in the same spot. By passing userId as a key to the Profile component, you’re asking React to treat two Profile components with different userId as two different components that should not share any state. Whenever the key (which you’ve set to userId) changes, React will recreate the DOM and reset the state of the Profile component and all of its children. Now the comment field will clear out automatically when navigating between profiles.


**Remove unnecessary Effects: Adjusting,Customization some state when a prop changes:**
Sometimes, you might want to reset or adjust a part of the state on a prop change, but not all of it.

This List component receives a list of items as a prop, and maintains the selected item in the selection state variable. You want to reset the selection to null whenever the items prop receives a different array:
```tsx
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // 🔴 Avoid: Adjusting state on prop change in an Effect
  useEffect(() => {
    setSelection(null);
  }, [items]);
  // ...
}
```
This, too, is not ideal. Every time the items change, the List and its child components will render with a stale selection value at first. Then React will update the DOM and run the Effects. Finally, the setSelection(null) call will cause another re-render of the List and its child components, restarting this whole process again.

Start by deleting the Effect. Instead, adjust the state directly during rendering:
```tsx
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // Better: Adjust the state while rendering
  const [prevItems, setPrevItems] = useState(items);
  if (items !== prevItems) {
    setPrevItems(items);
    setSelection(null);
  }
  // ...
}
```
**Storing information from previous renders** like this can be hard to understand, but it’s better than updating the same state in an Effect. In the above example, setSelection is called directly during a render. React will re-render the List immediately after it exits with a return statement. React has not rendered the List children or updated the DOM yet, so this lets the List children skip rendering the stale selection value.

When you update a component during rendering, React throws away the returned JSX and immediately retries rendering. To avoid very slow cascading retries, React only lets you update the same component’s state during a render. If you update another component’s state during a render, you’ll see an error. A condition like items !== prevItems is necessary to avoid loops. You may adjust state like this, but any other side effects (like changing the DOM or setting timeouts) should stay in event handlers or Effects to keep components pure.

Although this pattern is more efficient than an Effect, most components shouldn’t need it either. No matter how you do it, **adjusting state based on props or other state makes your data flow more difficult to understand and debug.** **Always check whether you can reset all state with a key or calculate everything during rendering instead.** For example, instead of storing (and resetting) the selected item, you can store the selected item ID:

```tsx
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selectedId, setSelectedId] = useState(null);
  // ✅ Best: Calculate everything during rendering
  const selection = items.find(item => item.id === selectedId) ?? null;
  // ...
}
```
Now there is no need to “adjust” the state at all. If the item with the selected ID is in the list, it remains selected. If it’s not, the selection calculated during rendering will be null because no matching item was found. This behavior is different, but arguably better because most changes to items preserve the selection.


**Remove unnecessary Effects: Sharing logic between event handlers :**
Let’s say you have a product page with two buttons (Buy and Checkout) that both let you buy that product. You want to show a notification whenever the user puts the product in the cart. Calling showNotification() in both buttons’ click handlers feels repetitive so you might be tempted to place this logic in an Effect:
```tsx
function ProductPage({ product, addToCart }) {
  // 🔴 Avoid: Event-specific logic inside an Effect
  useEffect(() => {
    if (product.isInCart) {
      showNotification(`Added ${product.name} to the shopping cart!`);
    }
  }, [product]);

  function handleBuyClick() {
    addToCart(product);
  }

  function handleCheckoutClick() {
    addToCart(product);
    navigateTo('/checkout');
  }
  // ...
}
```
This Effect is unnecessary. It will also most likely cause bugs. For example, let’s say that your app “remembers” the shopping cart between the page reloads. If you add a product to the cart once and refresh the page, the notification will appear again. It will keep appearing every time you refresh that product’s page. This is because product.isInCart will already be true on the page load, so the Effect above will call showNotification().

**When you’re not sure whether some code should be in an Effect or in an event handler, ask yourself why this code needs to run. Use Effects only for code that should run because the component was displayed to the user.** In this example, the notification should appear because the user pressed the button, not because the page was displayed! Delete the Effect and put the shared logic into a function called from both event handlers:
```tsx
function ProductPage({ product, addToCart }) {
  // ✅ Good: Event-specific logic is called from event handlers
  function buyProduct() {
    addToCart(product);
    showNotification(`Added ${product.name} to the shopping cart!`);
  }

  function handleBuyClick() {
    buyProduct();
  }

  function handleCheckoutClick() {
    buyProduct();
    navigateTo('/checkout');
  }
  // ...
}
```
This both removes the unnecessary Effect and fixes the bug.

**Remove unnecessary Effects: Sending a POST request:**
This Form component sends two kinds of POST requests. It sends an analytics event when it mounts. When you fill in the form and click the Submit button, it will send a POST request to the /api/register endpoint:
```tsx
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Good: This logic should run because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  // 🔴 Avoid: Event-specific logic inside an Effect
  const [jsonToSubmit, setJsonToSubmit] = useState(null);
  useEffect(() => {
    if (jsonToSubmit !== null) {
      post('/api/register', jsonToSubmit);
    }
  }, [jsonToSubmit]);

  function handleSubmit(e) {
    e.preventDefault();
    setJsonToSubmit({ firstName, lastName });
  }
  // ...
}
```
The analytics POST request should remain in an Effect. This is because the reason to send the analytics event is that the form was displayed. (It would fire twice in development, but see here for how to deal with that.)

However, the /api/register POST request is not caused by the form being displayed. You only want to send the request at one specific moment in time: when the user presses the button. It should only ever happen on that particular interaction. Delete the second Effect and move that POST request into the event handler:
```tsx
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Good: This logic runs because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  function handleSubmit(e) {
    e.preventDefault();
    // ✅ Good: Event-specific logic is in the event handler
    post('/api/register', { firstName, lastName });
  }
  // ...
}
```
When you choose whether to put some logic into an event handler or an Effect, the main question you need to answer is what kind of logic it is from the user’s perspective. **If this logic is caused by a particular interaction, keep it in the event handler.If it’s caused by the user seeing the component on the screen, keep it in the Effect.** 


**Remove unnecessary Effects: Chains of computations:**
Sometimes you might feel tempted to chain Effects that each adjust a piece of state based on other state:
```tsx
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);
  const [isGameOver, setIsGameOver] = useState(false);

  // 🔴 Avoid: Chains of Effects that adjust the state solely to trigger each other
  useEffect(() => {
    if (card !== null && card.gold) {
      setGoldCardCount(c => c + 1);
    }
  }, [card]);

  useEffect(() => {
    if (goldCardCount > 3) {
      setRound(r => r + 1)
      setGoldCardCount(0);
    }
  }, [goldCardCount]);

  useEffect(() => {
    if (round > 5) {
      setIsGameOver(true);
    }
  }, [round]);

  useEffect(() => {
    alert('Good game!');
  }, [isGameOver]);

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    } else {
      setCard(nextCard);
    }
  }

  // ...
```
There are two problems with this code.

One problem is that it is very inefficient: the component (and its children) have to re-render between each set call in the chain. In the example above, in the worst case (setCard → render → setGoldCardCount → render → setRound → render → setIsGameOver → render) there are three unnecessary re-renders of the tree below.

Even if it weren’t slow, as your code evolves, you will run into cases where the “chain” you wrote doesn’t fit the new requirements. Imagine you are adding a way to step through the history of the game moves. You’d do it by updating each state variable to a value from the past. However, setting the card state to a value from the past would trigger the Effect chain again and change the data you’re showing. Such code is often rigid and fragile.

In this case, it’s better to calculate what you can during rendering, and adjust the state in the event handler:
```tsx
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);

  // ✅ Calculate what you can during rendering
  const isGameOver = round > 5;

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    }

    // ✅ Calculate all the next state in the event handler
    setCard(nextCard);
    if (nextCard.gold) {
      if (goldCardCount <= 3) {
        setGoldCardCount(goldCardCount + 1);
      } else {
        setGoldCardCount(0);
        setRound(round + 1);
        if (round === 5) {
          alert('Good game!');
        }
      }
    }
  }
```
This is a lot more efficient. Also, if you implement a way to view game history, now you will be able to set each state variable to a move from the past without triggering the Effect chain that adjusts every other value. If you need to reuse logic between several event handlers, you can extract a function and call it from those handlers.

In some cases, **you can’t calculate the next state directly in the event handler.** For example, **imagine a form with multiple dropdowns where the options of the next dropdown depend on the selected value of the previous dropdown.** Then, a chain of Effects is appropriate because you are synchronizing with network.

**Remove unnecessary Effects: Initializing the application:**
Some logic should only run once when the app loads.

You might be tempted to place it in an Effect in the top-level component:
```tsx
function App() {
  // 🔴 Avoid: Effects with logic that should only ever run once
  useEffect(() => {
    loadDataFromLocalStorage();
    checkAuthToken();
  }, []);
  // ...
}
```
However, you’ll quickly discover that it runs twice in development. This can cause issues—for example, maybe it invalidates the authentication token because the function wasn’t designed to be called twice. In general, your components should be resilient to being remounted. This includes your top-level App component.

If some logic must run once per app load rather than once per component mount, add a top-level variable to track whether it has already executed:
```tsx
let didInit = false;

function App() {
  useEffect(() => {
    if (!didInit) {
      didInit = true;
      // ✅ Only runs once per app load
      loadDataFromLocalStorage();
      checkAuthToken();
    }
  }, []);
  // ...
}
```
You can also run it during module initialization and before the app renders:
```tsx
if (typeof window !== 'undefined') { // Check if we're running in the browser.
   // ✅ Only runs once per app load
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```
**Code at the top level runs once when your component is imported—even if it doesn’t end up being rendered. Keep app-wide initialization logic to root component modules like App.js or in your application’s entry point.**

**Remove unnecessary Effects: Notifying parent components about state changes:**
Let’s say you’re writing a Toggle component with an internal isOn state which can be either true or false. There are a few different ways to toggle it (by clicking or dragging). You want to notify the parent component whenever the Toggle internal state changes, so you expose an onChange event and call it from an Effect:
```tsx
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  // 🔴 Avoid: The onChange handler runs too late
  useEffect(() => {
    onChange(isOn);
  }, [isOn, onChange])

  function handleClick() {
    setIsOn(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      setIsOn(true);
    } else {
      setIsOn(false);
    }
  }

  // ...
}
```
The Toggle updates its state first, and React updates the screen. Then React runs the Effect, which calls the onChange function passed from a parent component. Now the parent component will update its own state, starting another render pass. **It would be better to do everything in a single pass.**

Delete the Effect and instead **update the state of both components within the same event handler:**
```tsx
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  function updateToggle(nextIsOn) {
    // ✅ Good: Perform all updates during the event that caused them
    setIsOn(nextIsOn);
    onChange(nextIsOn);
  }

  function handleClick() {
    updateToggle(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      updateToggle(true);
    } else {
      updateToggle(false);
    }
  }

  //
```
With this approach, both the Toggle component and its parent component update their state during the event. **React batches updates from different components together, so there will only be one render pass.**

You might also be able to remove the state altogether, and instead receive isOn from the parent component:
```tsx
// ✅ Also good: the component is fully controlled by its parent
function Toggle({ isOn, onChange }) {
  function handleClick() {
    onChange(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      onChange(true);
    } else {
      onChange(false);
    }
  }

  // ...
}
```
“Lifting state up” lets the parent component fully control the Toggle by toggling the parent’s own state. **Whenever you try to keep two different state variables synchronized, try lifting state up instead!**

**Remove unnecessary Effects: Passing data to the parent:**
This Child component fetches some data and then passes it to the Parent component in an Effect:
```tsx
function Parent() {
  const [data, setData] = useState(null);
  // ...
  return <Child onFetched={setData} />;
}

function Child({ onFetched }) {
  const data = useSomeAPI();
  // 🔴 Avoid: Passing data to the parent in an Effect
  useEffect(() => {
    if (data) {
      onFetched(data);
    }
  }, [onFetched, data]);
  // ...
}
```
In React, data flows from the parent components to their children. When you see something wrong on the screen, you can trace where the information comes from by going up the component chain until you find which component passes the wrong prop or has the wrong state. When child components update the state of their parent components in Effects, the data flow becomes very difficult to trace. Since both the child and the parent need the same data, let the parent component fetch that data, and pass it down to the child instead:
```tsx
function Parent() {
  const data = useSomeAPI();
  // ...
  // ✅ Good: Passing data down to the child
  return <Child data={data} />;
}

function Child({ data }) {
  // ...
}
```
This is simpler and keeps the data flow predictable: **the data flows down from the parent to the child.**



## Lifecycle of Reactive Effects
Effects have a different lifecycle from components. **Components may mount, update, or unmount**. An Effect can only **do two things: to start synchronizing something, and later to stop synchronizing it.** 

**Always focus on a single start/stop cycle at a time. It shouldn’t matter whether a component is mounting, updating, or unmounting. All you need to do is to describe how to start synchronization and how to stop it. If you do it well, your Effect will be resilient to being started and stopped as many times as it’s needed.**

**In development, React always remounts each component once.**

Every time after your component re-renders, React will look at the array of dependencies that you have passed. If any of the values in the array is different from the value at the same spot that you passed during the previous render, React will re-synchronize your Effect.

**Each Effect represents a separate synchronization process:**
Resist adding unrelated logic to your Effect only because this logic needs to run at the same time as an Effect you already wrote. For example, let’s say you want to send an analytics event when the user visits the room. You already have an Effect that depends on roomId, so you might feel tempted to add the analytics call there:
```tsx
function ChatRoom({ roomId }) {
  useEffect(() => {
    logVisit(roomId);
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
  // ...
}
```
But imagine you later add another dependency to this Effect that needs to re-establish the connection. If this Effect re-synchronizes, it will also call logVisit(roomId) for the same room, which you did not intend. **Logging the visit is a separate process from connecting. Write them as two separate Effects:**
```tsx
function ChatRoom({ roomId }) {
  useEffect(() => {
    logVisit(roomId);
  }, [roomId]);

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    // ...
  }, [roomId, newDeps]);
  // ...
}
```
**Each Effect in your code should represent a separate and independent synchronization process.**

In the above example, deleting one Effect wouldn’t break the other Effect’s logic. **This is a good indication that they synchronize different things, and so it made sense to split them up.** On the other hand, if you split up a cohesive piece of logic into separate Effects, the code may look “cleaner” but will be more difficult to maintain. This is why you should think whether the processes are same or separate, not whether the code looks cleaner.

**Effects “react” to reactive values(state variables).**

**All variables declared in the component body are reactive:**
Props and state aren’t the only reactive values. Values that you calculate from them are also reactive. If the props or state change, your component will re-render, and the values calculated from them will also change. This is why all variables from the component body used by the Effect should be in the Effect dependency list.

Let’s say that the user can pick a chat server in the dropdown, but they can also configure a default server in settings. Suppose you’ve already put the settings state in a context so you read the settings from that context. Now you calculate the serverUrl based on the selected server from props and the default server:
```tsx
function ChatRoom({ roomId, selectedServerUrl }) { // roomId is reactive
  const settings = useContext(SettingsContext); // settings is reactive
  const serverUrl = selectedServerUrl ?? settings.defaultServerUrl; // serverUrl is reactive
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId); // Your Effect reads roomId and serverUrl
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId, serverUrl]); // So it needs to re-synchronize when either of them changes!
  // ...
}
```
In this example, serverUrl is not a prop or a state variable. It’s a regular variable that you calculate during rendering. But it’s calculated during rendering, so it can change due to a re-render. This is why it’s reactive.

**All values inside the component (including props, state, and variables in your component’s body) are reactive. Any reactive value can change on a re-render, so you need to include reactive values as Effect’s dependencies.**

In other words, Effects “react” to all values from the component body.

**Effects are reactive blocks of code.** They re-synchronize when the values you read inside of them change. Unlike event handlers, which only run once per interaction, Effects run whenever synchronization is necessary.

**You can’t “choose” your dependencies. Your dependencies must include every reactive value you read in the Effect.**

**Avoid relying on objects and functions as dependencies. If you create objects and functions during rendering and then read them from an Effect, they will be different on every render. This will cause your Effect to re-synchronize every time.**

## Separating Events from Effects
- Event handlers only re-run when you perform the **same interaction again.**
- Unlike event handlers, **Effects re-synchronize if some value they read, like a prop or a state variable, is different from what it was during the last render.**
- Sometimes, **you also want a mix of both behaviors:** an Effect that re-runs in response to some values but not others.

**Choosing between event handlers and Effects:**</br>
- Event handlers run in response to specific interactions.
- Effects run whenever synchronization is needed. **Props, state, and variables declared inside your component’s body are called reactive values.** In this example, serverUrl is not a reactive value, but roomId and message are. They participate in the rendering data flow:
```tsx
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  // ...
}
```
**Reactive values** like these can change due to a re-render.

**Extracting non-reactive logic out of Effects:**</br>
Things get more tricky when you want to mix reactive logic with non-reactive logic.

For example, imagine that you want to show a notification when the user connects to the chat. You read the current theme (dark or light) from the props so that you can show the notification in the correct color.

However, theme is a reactive value (it can change as a result of re-rendering), and every reactive value read by an Effect must be declared as its dependency. Now you have to specify theme as a dependency of your Effect:
```tsx
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    return () => {
      connection.disconnect()
    };
  }, [roomId, theme]); // ✅ All dependencies declared
```
When the roomId changes, the chat re-connects as you would expect. But since theme is also a dependency, the chat also re-connects every time you switch between the dark and the light theme. That’s not great!

In other words, you don’t want this line to be reactive, even though it is inside an Effect (which is reactive):
```tsx
      showNotification('Connected!', theme);
```
**Declaring an Effect Event:**
Use a special Hook called useEffectEvent to extract this non-reactive logic out of your Effect:
```tsx
import { useEffect, useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });
  // ...
```
**Here, onConnected is called an Effect Event. It’s a part of your Effect logic, but it behaves a lot more like an event handler.** The logic inside it is not reactive, and **it always “sees” the latest values of your props and state.**
```tsx
function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification('Connected!', theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      onConnected();
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```
This solves the problem. Note that you had to remove onConnected from the list of your Effect’s dependencies. **Effect Events are not reactive and must be omitted from dependencies.**

**You can think of Effect Events as being very similar to event handlers.** The main difference is that **event handlers run in response to a user interactions**, **whereas Effect Events are triggered by you from Effects.** Effect Events let you “break the chain” between the reactivity of Effects and code that should not be reactive.

**Reading latest props and state with Effect Events:**
Effect Events let you fix many patterns where you might be tempted to suppress the dependency linter.

For example, say you have an Effect to log the page visits:
```tsx
function Page() {
  useEffect(() => {
    logVisit();
  }, []);
  // ...
}
```
Later, you add multiple routes to your site. Now your Page component receives a url prop with the current path. You want to pass the url as a part of your logVisit call, but the dependency linter complains:
```tsx
function Page({ url }) {
  useEffect(() => {
    logVisit(url);
  }, []); // 🔴 React Hook useEffect has a missing dependency: 'url'
  // ...
}
```
Think about what you want the code to do. You want to log a separate visit for different URLs since each URL represents a different page. In other words, this logVisit call should be reactive with respect to the url. This is why, in this case, it makes sense to follow the dependency linter, and add url as a dependency:
```tsx
function Page({ url }) {
  useEffect(() => {
    logVisit(url);
  }, [url]); // ✅ All dependencies declared
  // ...
}
```
Now let’s say you want to include the number of items in the shopping cart together with every page visit:
```tsx
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  useEffect(() => {
    logVisit(url, numberOfItems);
  }, [url]); // 🔴 React Hook useEffect has a missing dependency: 'numberOfItems'
  // ...
}
```
You used numberOfItems inside the Effect, so the linter asks you to add it as a dependency. However, you don’t want the logVisit call to be reactive with respect to numberOfItems. If the user puts something into the shopping cart, and the numberOfItems changes, this does not mean that the user visited the page again. In other words, visiting the page is, in some sense, an “event”. It happens at a precise moment in time.

Split the code in two parts:
```tsx
function Page({ url }) {
  const { items } = useContext(ShoppingCartContext);
  const numberOfItems = items.length;

  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    onVisit(url);
  }, [url]); // ✅ All dependencies declared
  // ...
}
```
As a result, you will call logVisit for every change to the url, and always read the latest numberOfItems. However, if numberOfItems changes on its own, this will not cause any of the code to re-run.

This becomes especially important if there is some asynchronous logic inside the Effect:
```tsx
  const onVisit = useEffectEvent(visitedUrl => {
    logVisit(visitedUrl, numberOfItems);
  });

  useEffect(() => {
    setTimeout(() => {
      onVisit(url);
    }, 5000); // Delay logging visits
  }, [url]);
```
Here, url inside onVisit corresponds to the latest url (which could have already changed), but visitedUrl corresponds to the url that originally caused this Effect (and this onVisit call) to run.

**Limitations of Effect Events:**
Effect Events are very limited in how you can use them:

- Only call them from inside Effects.
- Never pass them to other components or Hooks.

For example, don’t declare and pass an Effect Event like this:
```tsx
function Timer() {
  const [count, setCount] = useState(0);

  const onTick = useEffectEvent(() => {
    setCount(count + 1);
  });

  useTimer(onTick, 1000); // 🔴 Avoid: Passing Effect Events

  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  useEffect(() => {
    const id = setInterval(() => {
      callback();
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay, callback]); // Need to specify "callback" in dependencies
}
```
Instead, always declare Effect Events directly next to the Effects that use them:
```tsx
function Timer() {
  const [count, setCount] = useState(0);
  useTimer(() => {
    setCount(count + 1);
  }, 1000);
  return <h1>{count}</h1>
}

function useTimer(callback, delay) {
  const onTick = useEffectEvent(() => {
    callback();
  });

  useEffect(() => {
    const id = setInterval(() => {
      onTick(); // ✅ Good: Only called locally inside an Effect
    }, delay);
    return () => {
      clearInterval(id);
    };
  }, [delay]); // No need to specify "onTick" (an Effect Event) as a dependency
}
```
Effect Events are non-reactive “pieces” of your Effect code. They should be next to the Effect using them.

In general, you should be suspicious of functions like **onMount that focus on the timing rather than the purpose of a piece of code.**
It may feel “more descriptive” at first but it obscures your intent. 
As a rule of thumb, **Effect Events should correspond to something that happens from the user’s perspective.** For example, **onMessage, onTick, onVisit, or onConnected are good Effect Event names.** Code inside them would likely not need to be reactive. 
On the other hand, **onMount, onUpdate, onUnmount, or onAfterRender are so generic** that it’s easy to accidentally put code that should be reactive into them. This is why you should name your Effect Events after what the user thinks has happened, not when some code happened to run.


The Effect that had roomId set to "travel" (so it connected to the "travel" room) will show the notification for "travel". The Effect that had roomId set to "music" (so it connected to the "music" room) will show the notification for "music". In other words, connectedRoomId comes from your Effect (which is reactive), while theme always uses the latest value.

To solve the additional challenge, save the notification timeout ID and clear it in the cleanup function of your Effect:
```tsx
import { useState, useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';
import { createConnection, sendMessage } from './chat.js';
import { showNotification } from './notifications.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(connectedRoomId => {
    showNotification('Welcome to ' + connectedRoomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    let notificationTimeoutId;
    connection.on('connected', () => {
      notificationTimeoutId = setTimeout(() => {
        onConnected(roomId);
      }, 2000);
    });
    connection.connect();
    return () => {
      connection.disconnect();
      if (notificationTimeoutId !== undefined) {
        console.log('notificationTimeoutId',notificationTimeoutId);
        clearTimeout(notificationTimeoutId);
      }
    };
  }, [roomId]);

  return <h1>Welcome to the {roomId} room!</h1>
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Use dark theme
      </label>
      <hr />
      <ChatRoom
        roomId={roomId}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
```
This ensures that already scheduled (but not yet displayed) notifications get cancelled when you change rooms.


## Removing Effect Dependencies
- When you write an Effect, **the linter will verify that you’ve included every reactive value **(like props and state) that the Effect reads in the list of your Effect’s dependencies.
- **Unnecessary dependencies may cause your Effect to run too often, or even create an infinite loop.**

- **Effects “react” to reactive values. Since roomId is a reactive value (it can change due to a re-render),** the linter verifies that you’ve specified it as a dependency.
- If roomId receives a different value, React will re-synchronize your Effect. This ensures that the chat stays connected to the selected room and “reacts” to the dropdown:
```tsx
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';

const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
  return <h1>Welcome to the {roomId} room!</h1>;
}

export default function App() {
  const [roomId, setRoomId] = useState('general');
  return (
    <>
      <label>
        Choose the chat room:{' '}
        <select
          value={roomId}
          onChange={e => setRoomId(e.target.value)}
        >
          <option value="general">general</option>
          <option value="travel">travel</option>
          <option value="music">music</option>
        </select>
      </label>
      <hr />
      <ChatRoom roomId={roomId} />
    </>
  );
}
```

**To remove a dependency, prove that it’s not a dependency:**
To remove a dependency, “prove” to the linter that it doesn’t need to be a dependency. For example, you can move roomId out of your component to prove that it’s not reactive and won’t change on re-renders:
```tsx
const serverUrl = 'https://localhost:1234';
const roomId = 'music'; // Not a reactive value anymore

function ChatRoom() {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, []); // ✅ All dependencies declared
  // ...
}
```

**Removing unnecessary dependencies:**
Every time you adjust the Effect’s dependencies to reflect the code, look at the dependency list. **Does it make sense for the Effect to re-run when any of these dependencies change? Sometimes, the answer is “no”:**
- You might want to **re-execute different parts of your Effect under different conditions**
- You might want to only **read the latest value of some dependency instead of “reacting” to its changes.**
- A dependency may **change too often unintentionally because it’s an object or a function.**

**Should this code move to an event handler?**
Imagine a form. On submit, you set the submitted state variable to true. You need to send a POST request and show a notification. You’ve put this logic inside an Effect that “reacts” to submitted being true:
```tsx
function Form() {
  const [submitted, setSubmitted] = useState(false);

  useEffect(() => {
    if (submitted) {
      // 🔴 Avoid: Event-specific logic inside an Effect
      post('/api/register');
      showNotification('Successfully registered!');
    }
  }, [submitted]);

  function handleSubmit() {
    setSubmitted(true);
  }

  // ...
```
Later, you want to style the notification message according to the current theme, so you read the current theme. Since theme is declared in the component body, it is a reactive value, so you add it as a dependency:
```tsx
function Form() {
  const [submitted, setSubmitted] = useState(false);
  const theme = useContext(ThemeContext);

  useEffect(() => {
    if (submitted) {
      // 🔴 Avoid: Event-specific logic inside an Effect
      post('/api/register');
      showNotification('Successfully registered!', theme);
    }
  }, [submitted, theme]); // ✅ All dependencies declared

  function handleSubmit() {
    setSubmitted(true);
  }  

  // ...
}
```
By doing this, you’ve introduced a bug. Imagine you submit the form first and then switch between Dark and Light themes. The theme will change, the Effect will re-run, and so it will display the same notification again!

he problem here is that this shouldn’t be an Effect in the first place. You want to send this POST request and show the notification in response to submitting the form, which is a particular interaction. To run some code in response to particular interaction, put that logic directly into the corresponding event handler:
```tsx
function Form() {
  const theme = useContext(ThemeContext);

  function handleSubmit() {
    // ✅ Good: Event-specific logic is called from event handlers
    post('/api/register');
    showNotification('Successfully registered!', theme);
  }  

  // ...
}
```
Now that the code is in an event handler, it’s not reactive—so it will only run when the user submits the form.

**Is your Effect doing several unrelated things?**
The next question you should ask yourself is whether your Effect is doing several unrelated things.

Imagine you’re creating a shipping form where the user needs to choose their city and area. You fetch the list of cities from the server according to the selected country to show them in a dropdown:
```tsx
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  const [city, setCity] = useState(null);

  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]); // ✅ All dependencies declared
```
This is a good example of fetching data in an Effect. You are synchronizing the cities state with the network according to the country prop. You can’t do this in an event handler because you need to fetch as soon as ShippingForm is displayed and whenever the country changes.

Now let’s say you’re adding a second select box for city areas, which should fetch the areas for the currently selected city. You might start by adding a second fetch call for the list of areas inside the same Effect:
```tsx
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);

  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    // 🔴 Avoid: A single Effect synchronizes two independent processes
    if (city) {
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
    }
    return () => {
      ignore = true;
    };
  }, [country, city]); // ✅ All dependencies declared
```
However, since the Effect now uses the city state variable, you’ve had to add city to the list of dependencies. That, in turn, introduced a problem: when the user selects a different city, the Effect will re-run and call fetchCities(country). As a result, you will be unnecessarily refetching the list of cities many times.

**The problem with this code is that you’re synchronizing two different unrelated things.**
You can improve this code by **extracting repetitive logic into a custom Hook.**

**Do you want to read a value without “reacting” to its changes?**
Suppose that you want to play a sound when the user receives a new message unless isMuted is true:
```tsx
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      setMessages(msgs => [...msgs, receivedMessage]);
      if (!isMuted) {
        playSound();
      }
    });
    // ..
```
The problem is that every time isMuted changes (for example, when the user presses the “Muted” toggle), the Effect will re-synchronize, and reconnect to the chat. This is not the desired user experience! (In this example, even disabling the linter would not work—if you do that, isMuted would get “stuck” with its old value.)

To solve this problem, **you need to extract the logic that shouldn’t be reactive out of the Effect.** You don’t want this Effect to “react” to the changes in isMuted. Move this non-reactive piece of logic into an Effect Event:
```tsx
import { useState, useEffect, useEffectEvent } from 'react';

function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [isMuted, setIsMuted] = useState(false);

  const onMessage = useEffectEvent(receivedMessage => {
    setMessages(msgs => [...msgs, receivedMessage]);
    if (!isMuted) {
      playSound();
    }
  });

  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (receivedMessage) => {
      onMessage(receivedMessage);
    });
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```
- **Effect Events let you split an Effect into reactive parts (which should “react” to reactive values like roomId and their changes) and non-reactive parts (which only read their latest values, like onMessage reads isMuted).** 
- Now that you read isMuted inside an Effect Event, **it doesn’t need to be a dependency of your Effect.** As a result, the chat won’t re-connect when you toggle the “Muted” setting on and off, solving the original issue!


**Move dynamic objects and functions inside your Effect:**
```tsx
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // ✅ All dependencies declared
  // ...
```
Now that options is declared inside of your Effect, it is no longer a dependency of your Effect. Instead, the only reactive value used by your Effect is roomId. Since roomId is not an object or function, you can be sure that it won’t be unintentionally different.


## Reusing Logic with Custom Hooks

**Custom Hooks: Sharing logic between components:** 
- Now your components don’t have as much repetitive logic. 
- More importantly, the **code inside them describes what they want to do (use the online status!) rather than how to do it (by subscribing to the browser events).**

**Should all functions called during rendering start with the use prefix?:**
No. Functions that don’t call Hooks don’t need to be Hooks.

If your function doesn’t call any Hooks, avoid the use prefix. Instead, write it as a regular function without the use prefix. For example, useSorted below doesn’t call Hooks, so call it getSorted instead:
```tsx
// 🔴 Avoid: A Hook that doesn't use Hooks
function useSorted(items) {
  return items.slice().sort();
}

// ✅ Good: A regular function that doesn't use Hooks
function getSorted(items) {
  return items.slice().sort();
}
```
This ensures that your code can call this regular function anywhere, including conditions:
```tsx
function List({ items, shouldSort }) {
  let displayedItems = items;
  if (shouldSort) {
    // ✅ It's ok to call getSorted() conditionally because it's not a Hook
    displayedItems = getSorted(items);
  }
  // ...
}
```
You should give use prefix to a function (and thus make it a Hook) if it uses at least one Hook inside of it:
```tsx
// ✅ Good: A Hook that uses other Hooks
function useAuth() {
  return useContext(Auth);
}
```

You can extract the repetitive logic into this useFormInput custom Hook:
```tsx
mport { useState } from 'react';

export function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  const inputProps = {
    value: value,
    onChange: handleChange
  };

  return inputProps;
}

function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');
  // ...
```
This is why it works like declaring two separate state variables!

**Custom Hooks let you share stateful logic but not state itself.
Each call to a Hook is completely independent from every other call to the same Hook.**


**Passing reactive values between Hooks:**
The code inside your custom Hooks will re-run during every re-render of your component. This is why, like components, custom Hooks need to be pure. Think of custom Hooks’ code as part of your component’s body!

**When to use custom Hooks:**
You don’t need to extract a custom Hook for every little duplicated bit of code. Some duplication is fine. For example, extracting a useFormInput Hook to wrap a single useState call like earlier is probably unnecessary.


For example, consider a ShippingForm component that displays two dropdowns: one shows the list of cities, and another shows the list of areas in the selected city. You might start with some code that looks like this:
```tsx
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  // This Effect fetches cities for a country
  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]);

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  // This Effect fetches areas for the selected city
  useEffect(() => {
    if (city) {
      let ignore = false;
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [city]);
```
Now you can replace both Effects in the ShippingForm components with calls to useData:
```tsx
function ShippingForm({ country }) {
  const cities = useData(`/api/cities?country=${country}`);
  const [city, setCity] = useState(null);
  const areas = useData(city ? `/api/areas?city=${city}` : null);
  // ...
```
- However, whenever you write an Effect, consider whether it would be clearer to also wrap it in a custom Hook.
- Extracting a custom Hook makes the data flow explicit.
- By “hiding” your Effect inside useData, you also prevent someone working on the ShippingForm component from adding unnecessary dependencies to it.
- A good custom Hook makes the calling code more declarative by constraining what it does. For example, useChatRoom(options) can only connect to the chat room, while useImpressionLog(eventName, extraData) can only send an impression log to the analytics.
-  If your custom Hook API doesn’t constrain the use cases and is very abstract, in the long run it’s likely to introduce more problems than it solves.

- Similar to a design system, you might find it helpful to start extracting common idioms from your app’s components into custom Hooks.
- **This will keep your components’ code focused on the intent, and let you avoid writing raw Effects very often.** Many excellent custom Hooks are maintained by the React community.
- Effects let you connect React to external systems. The more coordination between Effects is needed (for example, to chain multiple animations), the more it makes sense to extract that logic out of Effects and Hooks completely like in the sandbox above. 








