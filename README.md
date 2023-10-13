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
    - [Forwarding props with the JSX spread syntax](#forwarding-props-with-the-jsx-spread-syntax)
    - [Passing JSX as children](#passing-jsx-as-children)
    - [Props vs State](#props-vs-state)
  - [Conditional rendering](#conditional-rendering)
    - [Conditionally returning nothing with null](#conditionally-returning-nothing-with-null)
    - [Conditionally including JSX](#conditionally-including-jsx)

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

















































