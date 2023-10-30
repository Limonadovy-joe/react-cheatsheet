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
  - [State components memory](#state-components-memory)
  - [State components memory](#state-components-memory)
  - [State components memory](#state-components-memory)
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



















































