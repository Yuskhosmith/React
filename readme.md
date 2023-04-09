# This is my Journey, Learning REACT JS

## Table Of Content 🚀

- [General Intro 🫥](#general-intro)
- [Components 👁️](#components)
- [React State ⛽](#react-state)
  - [Event Listener](#event-listener)
  - [State](#state)
  - [Unidirectional data flow (top to bottom) ↘️](#unidirectional-data-flow-from-top-to-bottom)
- [Rendering Conditional Contents 🤔](#rendering-conditional-contents)
- [Inline Styling](#inline-styling)
- [Dynamic Styling](#dynamic-styling)
- [Styled Components 💅🏾](#styled-components)
- [CSS Modules 💪🏾](#css-modules)
- [Debugging React Apps 🧑🏾‍💻](#debugging-react-apps)
- [Fragments, Portals & Refs](#fragments-portals--refs)
  - [JSX Limitations](#jsx-limitations)
  - [Fragments](#fragments)
  - [Portals](#portals)
  - [Refs](#refs)
  - [Controlled Vs Uncontrolled Components](#controlled-vs-uncontrolled-component)
- [Effects, Reducers & Context](#effects-reducers--context)
  - [Effect / Side Effect](#effect--side-effect)
  - [Reducers / useReducer](#reducers--usereducer)
  - [useState() vs useReducer()🥷🏾](#usestate-vs-usereducer)

## General Intro

- Don't touch the DOM. React will do it
- Build websites like lego blocks
- Unidirectional data flow (from top to bottom)
- REACT is just the UI, the rest is up to you and I

## Components

There can only be one parent-level component i.e

```jsx
return <div>Hello, I'm the CardList Component</div>;
```

There can't be anything after the div but you can put things inside. The above code is called JSX code (JavaScript XML).

Components help you not repeat yourself, build once and use it multiple times. Don't do too many things in one place, make it as small as possible.

React uses declarative programming as its programming paradigm which means it allows you to describe what the output looks like rather than what the process should look like. React will then convert it to vanillaJS which uses imperative programming paradigm to describe how the DOM should be, this happens during rendering.

Elements tags that start with lower cases are considered to be HTML tags and the ones that start with uppercases are components or custom components

Components can be named using the following:

```jsx
components>
    component1>
        component1.component.jsx
        compoenent1.style.css
    component2>
        component2.jsx
        component2.css
    component3>
        component3.js
        component3.css
    componet4.js/.jsx/.component.jsx
    component4.css/.style.css
```

It is better to create a folder for each component to keep things tidy

You can't have a component as a wrapper like the regular HTML tags e.g.

```jsx
// this is wrong.
return (
  <Component>
    <h1>Heading 1</h1>
    // the next two lines are the same
    <Component2></Component2>
    <Component2 />
  </Component>
);
```

But then again there's a way around it, react has a reserved prop name just like className, called `children`. It collects whatever is between the component opening tag and closing tag. Here's an example:

```jsx
// Component
function Component(props) {
  const classes = 'component ' + props.className;
  return <div className={classes}>{props.children}</div>;
}
```

Because reactjs doesn't allow the previous code, this code collects the props around the component and recreates it using itself. The `classes` constant is there so that the styling of the element if it were an HTML element, would be added to the new Component element. Why would we need this? To tackle code repetition, like in styling.

In pure react, the return function looks something like this

```jsx
import React from 'react';
import Expenses from ...;
return React.createElement(
  'div',
  {},
  React.createElement('h2', {}, 'Some H2 string'),
  React.createElement(Expenses, {propName: prop}),
);
```

The above code is the same in modern jsx as:

```jsx
import Expenses from ...;
return (
    <div>
        <h2>Some H2 string</h2>
        <Expenses propName={prop} />
    </div>
);
```

## React State

### Event Listener

When we have an event listener on an even it looks something like this

```jsx
const clickHandler = () => {
  console.log('clicked!');
};
return (
  <div>
    <button onClick={clickHandler}>Click</button>
  </div>
);

// we cant say
<button onClick={clickHandler()}>Click</button>;
// this will execute the function when the div is rendered, which is one step too early.
```

### State

If there's data that might change, and the change would reflect on the user interface, that is when we use state. For example:

```jsx
import { useState } from 'react';

const Component = (props) => {
  const [title, setTitle] = useState(props.title);
  const clickHandler = () => {
    setTitle('Updated!');
  };

  return (
    <div>
      <h2>{title}</h2>
      <button onClick={clickHandler}>Change Title</button>
    </div>
  );
};

export default Component;
```

The useState function/method should be directly inside the component function and not in a subfunction, the data that is bound to change is the parameter being passed to it. The useState function re-renders that particular component using the new title that's being set, but the title variable doesn't change even after the setTitle function i.e. it still holds the old value until the state is rendered.

If we have to use multiple states on the same component we can as well group them together like this;

```jsx
import { useState } from 'react';

const Component = (props) => {
  // const [enteredTitle, setEnteredTitle] = useState('');
  // const [enteredAmount, setEnteredAmount] = useState('');
  // const [enteredDate, setEnteredDate] = useState('');
  const [userInput, setUserInput] = useState({
    enteredTitle: '',
    enteredAmount: '',
    enteredDate: '',
  });
  const titleChangeHandler = (e) => {
    // setEnteredTitle(e.target.value);
    setUserInput({
      ...userInput,
      enteredTitle: e.target.value,
    });
  };

  const amountChangeHandler = (e) => {
    // setEnteredAmount(e.target.value);
    setUserInput({
      ...userInput,
      enteredAmount: e.target.value,
    });
  };

  const dateChangeHandler = (e) => {
    // setEnteredDate(e.target.value);
    setUserInput({
      ...userInput,
      enteredDate: e.target.value,
    });
  };
};
```

The comments are what it would look like without grouping the state.

Whenever you update a state and you depend on the previous state, using the above is not okay... This is better

```jsx
import { useState } from 'react';

const Component = (props) => {
  const [userInput, setUserInput] = useState({
    enteredTitle: '',
    enteredAmount: '',
    enteredDate: '',
  });
  const titleChangeHandler = (e) => {
    setUserInput((prevState) => {
      return { ...prevState, enteredTitle: e.target.value };
    });
  };

  const amountChangeHandler = (e) => {
    setUserInput((prevState) => {
      return { ...prevState, enteredAmount: e.target.value };
    });
  };

  const dateChangeHandler = (e) => {
    setUserInput((prevState) => {
      return { ...prevState, enteredDate: e.target.value };
    });
  };
};
```

In many cases both will work fine but react schedules state updates, it doesn't perform them instantly. If you schedule a lot pf state updates at the same time you could be depending on an outdated or an incorrect state snapshot if you use the first group approach, but in the second one, React will guarantee that the snapshot is the latest keeping all schedule state updates in mind.

We can just continue using the single state as it is preferred. But keep it in mind to use the function inside the setUserInput approach when a state is dependent on the other.

### Unidirectional data flow (from top to bottom)

React goes from the App component down to the last div component. But what if we want to pass data from a component below to a component above, so that can use the data in the component above? We can do that by passing the function from the component above and executing it in the component below. This is known as **'Lifting State Up'**.

## Rendering Conditional Contents

When the data being rendered is dependent on some kind of conditions we can use a tenary operation to do that:

```jsx
{
  x > 0 ? y : b;
}
//basically if x is greater than 0 do y else do b
```

we can also use this

```jsx
{
  x > 0 && y;
}
//basically if x is greater than 0 do y

{
  x <= 0 && b;
}
//if x is less than or equal 0 do b
```

We can store the jsx code inside a variable and then use it in our return function after running our logic through it like:

```jsx
const Component = () => {
  let content = <p>Some Paragraph</p>;

  if (x <= 0) {
    content = <p>No Content Found</p>;
  }

  return <div>{content}</div>;
};

export default Component;
```

And then again we can use the conditional content logic as a component on its own. If we use it as a component on it's own we can have multiple return statements based on each logic. E.g:

```jsx
const Component = ({ x }) => {
  if (x > 0) {
    return <p>Some Paragraph</p>;
  }

  return (
    <div>
      <p>No Content Found</p> // some other things specific to this condition
    </div>
  );
};

export default Component;
```

We can also render based on if a state is available or not, e.g.

```jsx
import { useState } from 'react';

const Component = () => {
  const [x, setX] = useState();

  // 'x is here' would be displayed on the page if this condition is met.
  if (someCondition) {
    setX({ x: 'x', y: 'y' });
  }

  return (
    <div>
      {x && <p>X is here</p>}
      <p>Some Paragraph</p>
    </div>
  );
};

export default Component;
```

In the jsx code, we're basically saying if x is defined(not undefined, as the default empty state assigns undefined to the variable), display this paragraph.

---

Setting fallbacks - when you expect certain parameters but none is given, you can set a default. e.g.

```jsx
import styles from './button.module.css';

const Button = (props) => {
  return (
    <button
      type={props.type || 'button'}
      className={styles.button}
      onClick={props.onClick}
    >
      {props.children}
    </button>
  );
};

export default Button;
```

In this button component, when the type isn't given as part of the props, it uses type='button' by default.

---

## Inline Styling

Styling can be done in jsx just like in normal HTML syntax but this time, the `style` attribute/prop takes an object like this:

```jsx
boxHeight = '20%'
<div className="box" style={{height: boxHeight, width: boxHeight}}></div>

// Or

<div className="box" style={{height: '20%'}}></div>

// When the CSS-property name has a hyphen(-) in it we can use camel case instead or put it in a string

<div className="box" style={{backgroundColor: 'red'}}></div>

// or

<div className="box" style={{'background-color': 'red'}}></div>

```

## Dynamic Styling

We can change the styling of elements dynamically, we can do this with inline css e.g.

```jsx
import React, { useState } from 'react';

import Button from '../../UI/Button/Button';
import './CourseInput.css';

const CourseInput = (props) => {
  const [enteredValue, setEnteredValue] = useState('');
  const [isValid, setIsValid] = useState(true);

  const goalInputChangeHandler = (event) => {
    if (event.target.value.trim().length > 0) {
      setIsValid(true);
    }
    setEnteredValue(event.target.value);
  };

  const formSubmitHandler = (event) => {
    event.preventDefault();
    if (enteredValue.trim().length === 0) {
      setIsValid(false);
      return;
    }
    props.onAddGoal(enteredValue);
  };

  return (
    <form onSubmit={formSubmitHandler}>
      <div className='form-control'>
        <label style={{ color: !isValid ? 'red' : 'black' }}>Course Goal</label>
        <input
          style={{
            borderColor: !isValid ? 'red' : 'black',
            background: !isValid ? 'salmon' : 'transparent',
          }}
          type='text'
          onChange={goalInputChangeHandler}
        />
      </div>
      <Button type='submit'>Add Goal</Button>
    </form>
  );
};

export default CourseInput;
```

From the code above the following are using the inline css to change the CSS depending on if the user submits an empty field or not

```jsx
// Using ternary operation
<label style={{ color: !isValid ? 'red' : 'black' }}>Course Goal</label>
<input
  style={{
    borderColor: !isValid ? 'red' : 'black',
    background: !isValid ? 'salmon' : 'transparent',
  }}
  type='text'
  onChange={goalInputChangeHandler}
/>
```

We can also change the style dynamically by writing our CSS in the css file and adding the needed classes to the elements. Referencing the code above we can rewrite it as:

```CSS
/* ...the CSS we have written inline in the jsx */
.form-control.invalid input {
  border-color: red;
  background: #ffd7d7;
}

.form-control.invalid label {
  color: red;
}

/* Notice there's no space between the two classes, that is indicating that the two classes must be on the same element for the css to be applied */
```

Our Jsx from above will now return this

```jsx
return (
  <form onSubmit={formSubmitHandler}>
    <div className={`form-control ${!isValid ? 'invalid' : ''}`}>
      <label>Course Goal</label>
      <input
        type='text'
        onChange={goalInputChangeHandler}
      />
    </div>
    <Button type='submit'>Add Goal</Button>
  </form>
);

// using string literal to change the class Name, in some instance it'll be 'form-control' in others it will be 'form-control invalid' and notice that curly brace before the className
```

## Styled Components

Being able to set styles that are not globally scoped will give us less stress when compared to the normal styling of components. Take for example I have styled a card item to have a max-width of 20px and in another component, there was another use of the className 'item', by default that element will have a max-width of 20px because we already set it in another component. Hence, the need for styled-components.

Styled Components is a package that helps build components that have certain styles attached to them, where the styles really only affect the component to which they are attached and not any other component.

To use it, you'll install the package in the project folder by typing:

```
npm install --save styled-components
```

And in a component that renders a button, we'll have:

```jsx
import styled from 'styled-components';

const Button = styled.button`
  font: inherit;
  padding: 0.5rem 1.5rem;
  border: 1px solid #8b005d;
  color: white;
  background: #8b005d;
  box-shadow: 0 0 4px rgba(0, 0, 0, 0.26);
  cursor: pointer;

  &:focus {
    outline: none;
  }

  &:hover,
  &:active {
    background: #ac0e77;
    border-color: #ac0e77;
    box-shadow: 0 0 8px rgba(0, 0, 0, 0.26);
  }
`;
export default Button;
```

`styled.button` is a method called tact template because it uses the \` \` instead of the normal ().

styled.element - supports all HTML tags, e.g. `styled.p`, `styled.a`, `styled.h1` e.t.c.

instead of using .classname{/_style_/} we will just the styles directly and if we need to do pseudo-class things or check if there's a particular className on the element, we use ampersand `&`. And for media queries, we do the same, instead of using the classname or any selector we just write the CSS inside the `@media` block.

So how does this work, basically the `styled.button` creates the button element and generates a unique class name and appends the CSS to the global CSS.

When we want to use `styled components` on a nested element i.e. an element that wraps another element, we can also write the component for it inside the same component file e.g.

```jsx
import styled from 'styled-components';

const StyledDiv = styled.div`
  width: 80%;
  background: blue;

  &.new-class {
    color: white;
  }
  @media (min-width: 768px) {
    width: 60%;
  }
`;

const Component = (props) => {
  // some other code
  return (
    <div>
      <StyledDiv>Some other code</StyledDiv>
    </div>
  );
};
export default Component;
```

we can also pass in props to the StyledDiv, including class name e.g.

```jsx
<StyledDiv className='new-class'>
  Some other code
</StyledDiv>
// or dynamic styling
<StyledDiv className={!valid && 'new-class'}>
  Some other code
</StyledDiv>
```

Styled components also support dynamic styling by collecting a prop that either points to True or False, e.g.

```jsx
import styled from 'styled-components';

const StyledDiv = styled.div`
  width: 80%;
  background: ${(props) => (props.valid ? 'blue' : 'red')};

  &.new-class {
    color: white;
  }
  @media (min-width: 768px) {
    width: 60%;
  }
`;

const Component = (props) => {
  // some other code
  return (
    <div>
      <StyledDiv valid={true}>Some other code</StyledDiv>
    </div>
  );
};
export default Component;
```

## CSS Modules

CSS Modules allow us to use the same CSS class name in different files without worrying about naming clashes. CSS Modules allow the scoping of CSS by automatically creating a unique className of the format `[filename]_classname__hash`.This is almost the same as styled-components but the difference is the naming convention and how clean our component is. To use CSS modules in react we save our CSS file as `[name].module.css`.

Now, instead of importing `./name.css` or `name.style.css`, we'll say:

```jsx
import styles from './name.module.css';
```

Then, instead of saying `className='example'`, we'll say:

```jsx
import styles from './name.module.css';
const Component = (props) => {
  return <div className={styles.example}>...</div>;
};

export default Component;
```

or

```jsx
<div className={styles['example']}>...</div>
```

The classname that appears in our Dom will be:

```html
<div class="Component_example__hash">
  <!-- hash - any unique random string e.g. 2lkg-->
  ...
</div>
```

This is the best so far.

## Debugging React Apps

Just like how we debug python scripts in VSCode by adding breakpoints, we can also debug react apps in the browser by going to the source tabs and navigating to our original code which is usually in `static/js/user/` and also add breakpoints in them to see how data flow through the app.

We can also debug react apps by installing the `React Developer Tools` extension in our browser. In our dev tools, when we click on the `>>` button we should see two new items `components` and `profiler`.

In the `components` tab we can hover over each component and they will be highlighted on the page, we can also click on each component to see all the props in it, how it's rendered from component to App (top to bottom), hooks(more on this later) and as well the source of the component.

## Fragments, Portals & Refs

### JSX Limitations

In React, we are not allowed to return two or more adjacents elements, therefore there can be only one parent element in every component, which most of the time is a `div`. This is a normal programming paradigm, we can't return multiple variables or values unless it's in an array or maybe a dictionary/javascript object. This is also possible in react, we can return the elements and components as an array, but we must give each item a key, so it'll be rendered efficiently, e.g.

```jsx
import SomeOtherComponent from '../SomeOtherComponent/SomeOtherComponent.component';
const Component = (props) => {
  // some other code
  return [<p key='p1'>A paragraph</p>, <SomeOtherComponent key='p2' />];
};
export default Component;
```

But it seems easier to just wrap the jsx in a div instead of returning an array of jsx. But that also creates a problem when we are building large applications called `div soup`, our final dom will be looking something like this:

```html
<div>
  <div>
    <div>
      <div>
        <div>
          <h2>Some Content - yeah, this can really happen</h2>
        </div>
      </div>
    </div>
  </div>
</div>
```

This can break styling when we use nested CSS selectors, but even if it doesn't this isn't a good practice, this might make the application slower because the browser needs to render each individual element and react also will be checking for state change (instead of re-rendering just the needed element, we might end up rendering a bunch of unnecessary divs as well). The wrapping element approach or wrapping div approach is okay but not ideal.

We can get around this by creating a helper component called wrapper. In your component folder:

```s
componet>
  helpers>
    wrapper.component.jsx
```

In the compoenet, we'll have the following code:

```js
const Wrapper = (props) => {
  return props.children;
};
export default Wrapper;
```

This basically collects the children that are inside the component as an array and returns it, so, no extra div is created, just what is needed. Our example from above will now be:

```jsx
import SomeOtherComponent from '../SomeOtherComponent/SomeOtherComponent.component';
import Wrapper from '../helpers/wrapper.component';
const Component = (props) => {
  // some other code
  return (
    <Wrapper>
      <p>A paragraph</p>
      <SomeOtherComponent />
    </Wrapper>
  );
};
export default Component;
```

### Fragments

We don't need to create the wrapper component on our own, it comes with react. There, it is called the **Fragment Component** and it looks like this:

```jsx
import React from 'react';
// some other code and component declaration
return (
  <React.Fragment>
    <p>A paragraph</p>
    <SomeOtherComponent />
  </React.Fragment>
);

// Or
import { Fragment } from 'react';
// some other code and component declaration
return (
  <Fragment>
    <p>A paragraph</p>
    <SomeOtherComponent />
  </Fragment>
);
```

or we could use this, but this depends on the project setup, the build workflow needs to support this - newer version of react supports this:

```jsx
return (
  <>
    <p>A paragraph</p>
    <SomeOtherComponent />
  </>
);
```

### Portals

While Fragments allow us to write cleaner code, to end up with less unnecessary HTML elements on the final-page / DOM. React Portals are another useful feature, which does something similar, which also helps us write cleaner code. Let's consider the following code:

```jsx
return (
  <React.Fragment>
    <MyModal />
    <MyInputForm />
  </React.Fragment>
);
```

This might render the following on the DOM:

```html
<section>
  <h2>Some other content</h2>
  <!-- Above Component starts here -->
  <div class="my-modal">
    <h2>A modal title!</h2>
  </div>
  <form>
    <label>Username</label>
    <input type="text" />
  </form>
</section>
```

There's nothing wrong with this code but because it works doesn't mean it's right, A modal is an overlay that covers the entire page which means the modal is not well placed... Similarly, we can style a div to be a button but it's not okay because of accessibilty and some other possible styling issues. Let's look at some example:

```jsx
import styles from './invalid.module.css';
import Container from '../container/container.component';
import Button from '../button/button.component';

const Invalid = ({ title, message, onClickHandler }) => {
  return (
    <>
      <div
        className={styles.backdrop}
        onClick={onClickHandler}
      />
      <Container className={styles.modal}>
        <header className={styles.header}>
          <h2>{title || 'Invalid Input'}</h2>
        </header>
        <div className={styles.content}>
          <p>{message}</p>
        </div>
        <footer className={styles.actions}>
          <Button onClick={onClickHandler}>Okay</Button>
        </footer>
      </Container>
    </>
  );
};

export default Invalid;
```

This is an error modal that just appears above the form and because of styling it works okay. But with portal we can put it directly below the body tag, we'll have it like this:

- In `index.html` we'll create new divs with ids to be able to identify it on the DOM under the `<body>` tag.
  ```html
  <div id="backdrop-root"></div>
  <div id="overlay-root"></div>
  ```
- In the Error Modal(Invalid Component), we'll split up the component into two, one being `Backdrop` and the other being `ModalOverlay`:

  ```jsx
  const Backdrop = ({ onClickHandler }) => {
    return (
      <div
        className={styles.backdrop}
        onClick={onClickHandler}
      />
    );
  };

  const ModalOverlay = ({ title, message, onClickHandler }) => {
    return (
      <Container className={styles.modal}>
        <header className={styles.header}>
          <h2>{title || 'Invalid Input'}</h2>
        </header>
        <div className={styles.content}>
          <p>{message}</p>
        </div>
        <footer className={styles.actions}>
          <Button onClick={onClickHandler}>Okay</Button>
        </footer>
      </Container>
    );
  };

  const Invalid = ({ title, message, onClickHandler }) => {
    return ();
  };
  ```

- Then, we'll import `react-dom`, by saying `import anyname from 'react-dom';` but we'll go with:
  ```jsx
  import ReactDOM from 'react-dom';
  ```
- Now, we'll use the ReactDOM object and it's methods to re-create our DOM is a more accessible way. Like this:

  ```jsx
  const Invalid = ({ title, message, onClickHandler }) => {
    return (
      <>
        {ReactDOM.createPortal(
          <Backdrop onClickHandler={onClickHandler} />,
          document.getElementById('backdrop-root')
        )}
        {ReactDOM.createPortal(
          <ModalOverlay
            title={title}
            message={message}
            onClickHandler={onClickHandler}
          />,
          document.getElementById('overlay-root')
        )}
      </>
    );
  };
  ```

  This is the same way the `index.js` renders the `App` component or used to render the `App` component.

That's the idea of portals - the rendered HTML code is moved somewhere else... literally portals 😂.

### Refs

With Fragments and Portals we can write cleaner HTML code and semantically correct code. Now, Refs - short for `Reference` - in their most basic form allows us to get access to other DOM elements and work with them.

For example, updating the state of an input field after every keystroke is a little redundant, this redundancy can be solved using refs, here is an example:

```jsx
import { useState } from 'react';

const FormInput = () => {
  const [eneteredInput, setEnteredInput] = useState('');

  const inputFieldOnChnageHandler = (event) => {
    setEnteredInput(event.target.value);
  };

  const formSubmitHandler = (event) => {
    event.preventDefault();
    // collect entered input and use it then set input to an empty string
    setEnteredInput('');
  };

  return (
    <form onSubmit={formSubmitHandler}>
      <input
        type='text'
        value={enteredInput}
        onChange={inputFieldOnChnageHandler}
      />
      <button type='submit'>Submit</button>
    </form>
  );
};

export default FormInput;
```

The code above uses `useState` and the state is updated on every keystroke made on the input field we can improve that using Refs and collect the value just when the user submits the form.

```jsx
import { useRef } from 'react';

const FormInput = () => {
  const enteredInputRef = useRef();

  const formSubmitHandler = (event) => {
    event.preventDefault();
    // collect entered input
    enteredInput = enteredInputRef.current.value;
    // use it then set input to an empty string
    enteredInputRef.current.value = '';
  };

  return (
    <form onSubmit={formSubmitHandler}>
      <input
        type='text'
        ref={enteredInputRef}
      />
      <button type='submit'>Submit</button>
    </form>
  );
};

export default FormInput;
```

As seen, useRef is much better when we're just collecting the values from users and moving on. It's also not advisable to manipulate the dom as we did with this line of code `enteredInputRef.current.value = '';`.

\*\* Refs can be used on any element.

### Controlled vs Uncontrolled Component

In the first approach the above code we used `useState` and we updated the state on every keystroke and fed it back to the input field with the value prop, this is known as **controlled approach/component** (because it's possible for us to change what's inputed with react). In the second approach where we used `useRef`, this approach is called **uncontrolled approach/component**, because we don't know what's going on in the browser, we just collect the final result that is in the input field when the form is submitted, it's also not possible to manipulate the data with react but we can change the DOM with the DOM API.

## Effects, Reducers & Context

### Effect / Side Effect
The Job of React is to Render some UI & allow the users to interact with the UI. For this to happen we want to
- Evaluate and Render JSX
- Manage State & Props
- React to Events & Inputs
- Re-evaluate Component upon State & Prop Changes.
All these are baked into React (i.e. useState(), Hook, Props, etc). Therefore, **Side Effects / Effects** are everything else that's happening in the application, e.g. sending HTTP request, store data in browser storage e.t.c. These are tasks that must happen outside the normal component evaluation (outside the component function). This is because the component is re-rendered when there's a change in state, if the http request is inside the component, then it'll also be executed again which might then lead to an infinite loop. To fix this, we have a tool for handling side effects called **useEffect() Hook**. The useEffect hook takes two arguments, the first one is the function that should be executed after every component evaluation, If the specified dependencies changed, the second argument is the specified dependencies:
```jsx
useEffect(() => { ... }, [ dependencies ]);
```

Let's take an expample of a login page, if the user logs in a value is saved to the browser's local storage and they see a welcome page, if we refresh we can check if the user is logged(from the local storage data) and the welcome page shows up:
```jsx
function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  const storedInfo = localStorage.getItem('isLoggedIn')

  if (storedInfo === '1') {
    setIsLoggedIn(true);
  }

  const loginHandler = (email, password) => {
    localStorage.setItem('isLoggedIn', '1');
    setIsLoggedIn(true);
  };

  const logoutHandler = () => {
    localStorage.removeItem('isLoggedIn');
    setIsLoggedIn(false);
  };

  return (
    <React.Fragment>
      <MainHeader isAuthenticated={isLoggedIn} onLogout={logoutHandler} />
      <main>
        {!isLoggedIn && <Login onLogin={loginHandler} />}
        {isLoggedIn && <Home onLogout={logoutHandler} />}
      </main>
    </React.Fragment>
  );
}

export default App;
```
The propblem with the above code is that, we have created an infinite loop, whenever there's a change of state or the changeState function is executed(setState e.g. `setIsLoggedIn(true);`) the component is re-rendered. To fix the above we'll use Effects (useEffect 😂):
```jsx
import React, { useState, useEffect } from 'react';

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  useEffect(() => {
    const storedInfo = localStorage.getItem('isLoggedIn')

    if (storedInfo === '1') {
      setIsLoggedIn(true);
    }
  }, [])
  const loginHandler = (email, password) => {
    // We should of course check email and password
    // But it's just a dummy/ demo anyways
    localStorage.setItem('isLoggedIn', '1');
    setIsLoggedIn(true);
  };

  const logoutHandler = () => {
    localStorage.removeItem('isLoggedIn');
    setIsLoggedIn(false);
  };

  return (
    <React.Fragment>
      <MainHeader isAuthenticated={isLoggedIn} onLogout={logoutHandler} />
      <main>
        {!isLoggedIn && <Login onLogin={loginHandler} />}
        {isLoggedIn && <Home onLogout={logoutHandler} />}
      </main>
    </React.Fragment>
  );
}

export default App;

```
With the useEffect function, the function only runs once when our app starts or is refreshed because the dependecy doesn't change/there's no dependency(`[]`).

Another use case for useEffect is when we have an input field, lets say we have an email and password field in a login form, and we check if the form is valid for every keystroke the user inputs in both fields, we can do that once by using `useEffect` and setting its dependencies as the values of the input field, so the code runs when either of them change:
```jsx
useEffect(() => {
  setFormIsValid(
    enteredEmail.includes('@') && enteredPassword.trim().length > 6
  );
}, [enteredEmail, enteredPassword])
```

To clear the confusion from above where I defined side effects as everything else that's not react, well, listening to changes on every keystroke and saving the data and also triggering a function based on that change is also a side effect... It's a side effect of user entering the data. Whenever there's an action that should be executed in response to some other action, it's a side effect.

One more thing about `useEffect`, sometimes we'll have an effect that'll need to do some clean up work (i.e. work done after the effect is executed 🌚). Using the `useEffect()` from above, we are excuting the function when each dependency is altered (i.e on every keystroke), what if we were sending http request for each keystrok, that'll be alot and might make the program very slow, we can avoid that by collecting a certain amount of keystroke or wait for a pause of certain time duration before collecting the data (debouncing - excute some action when there's a pause during typing). 
```jsx
useEffect(() => {
  setTimeout(() => {
    setFormIsValid(
    enteredEmail.includes('@') && enteredPassword.trim().length > 6
    );
  }, 500);
  
}, [enteredEmail, enteredPassword])

```
The above code only creates a 500 milliseconds delay on the execution of `setFormIsValid` which means it still basically executes on every keystroke but with a 500ms delay after each keystroke. We can improve this by resetting the timer after every keystroke, so when there's an actual pause is the only time the function executes. We'll be returning an anonymous function which is called **a cleanup function** and that will execute just before `useEffect` executes its function the next time (it doesn't run on the first execution but on every other execution):
```jsx
useEffect(() => {
  const timer = setTimeout(() => {
    setFormIsValid(
    enteredEmail.includes('@') && enteredPassword.trim().length > 6
    );
  }, 500);

  return () => {
    clearTimeout(timer)
  };
}, [enteredEmail, enteredPassword])

```
So, basically, the cleanup function clears the timer from the last effect execution, then set a new timer until we get a situation where the timeout is reached, then the function in the `useEffect` is executed. So, in instances where we send HTTP requests, this will save us a bunch of time and help increase the efficiency of the app.

Also, we have seen that we can pass an empty list of dependecies and that will make the `useEffect()` function run just once, we can also not include the list at all to make the `useEffect` function run every time the component re-renders i.e. for every state change.
```jsx
useEffect(() => {
  console.log('Effect that runs for every state change!');
})
```
PS: If there are no dependencies i.e `[]` and there's a cleanup function, the cleanup function runs when the component is removed from the DOM:
```jsx
useEffect(() => {
  console.log('Effect that runs once because there\'s no change in dependecies!');
  return ()=> {
    console.log('Cleanup after component is removed from the DOM');
  }
}, [])
```

### Reducers / useReducer()
Basically, useReducer is useState Promax 😂, it helps us with state management, it's a bit like useState but with more capabilities and used for more complex state. For example multiple states that belongs together that are managing the same thing but different aspect of it or multiple states that are related or change together, in these cases useState becomes error-prone and inefficient. useReducer() is an alternative to useState for more complex and powerful states.

When we update a state which depends on another state, then merging the states into one could be a good idea, using objects but this might become more complex as the app grows, so it's adivised to use useReducer.

useReducer also always returns an array with exactly two values and we can use array destructuring as we did with useState to get these values and store them in separate constants.
```jsx
const [state, dispatchFn] = useReducer(reducerFn, initialState, initFn);
```
- state -> Latest state snapshot like in useState
- dispatchFn -> A function that allows you to update the state snapshot but instead of just setting a new state value, we'll dispatch an action that will be consumed by the first argument we passed to `useReducer()` i.e reducerFn
- reducerFn -> A function that gets the latest state snapshot automatically because it'll be called by react and it gets the action that was dispatched and returns the updated state
- initialState -> literally, initial state, the starting state e.t.c.
- initFn -> this is a function that sets the inital state, in more complex situation like the result of HTTP request e.t.c.

Considering our previous use case in `useEffect` where we can have an email and password field and also their validity state:

```jsx
import React, { useState, useEffect } from 'react';

import Card from '../UI/Card/Card';
import classes from './Login.module.css';
import Button from '../UI/Button/Button';

const Login = (props) => {
  const [enteredEmail, setEnteredEmail] = useState('');
  const [emailIsValid, setEmailIsValid] = useState();
  const [enteredPassword, setEnteredPassword] = useState('');
  const [passwordIsValid, setPasswordIsValid] = useState();
  const [formIsValid, setFormIsValid] = useState(false);

  useEffect(() => {
    const timer = setTimeout(() => {
      // console.log('Effect')
      setFormIsValid(emailState.isValid && passwordState.isValid);
    }, 500);

    return () => {
      // console.log('CLEANUP');
      clearTimeout(timer);
    };
  }, [emailState, passwordState]);
  const emailChangeHandler = (event) => {
    setEnteredEmail(event.target.value);
  };

  const passwordChangeHandler = (event) => {
    setEnteredPassword(event.target.value);
  };

  const validateEmailHandler = (event) => {
    setEmailIsValid(event.target.value.includes('@'));
  };

  const validatePasswordHandler = (event) => {
    setPasswordIsValid(event.target.value.trim().length > 6);
  };

  const submitHandler = (event) => {
    event.preventDefault();
    props.onLogin(enteredEmail, enteredPassword);
  };

  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <div
          className={`${classes.control} ${
            emailIsValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor='email'>E-Mail</label>
          <input
            type='email'
            id='email'
            value={enteredEmail}
            onChange={emailChangeHandler}
            onBlur={validateEmailHandler}
          />
        </div>
        <div
          className={`${classes.control} ${
            passwordIsValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor='password'>Password</label>
          <input
            type='password'
            id='password'
            value={enteredPassword}
            onChange={passwordChangeHandler}
            onBlur={validatePasswordHandler}
          />
        </div>
        <div className={classes.actions}>
          <Button
            type='submit'
            className={classes.btn}
            disabled={!formIsValid}
          >
            Login
          </Button>
        </div>
      </form>
    </Card>
  );
};

export default Login;

```
The above code has similar states that can be combined into one using `useReducer()`, the `enteredEmail` & `emailIsValid` states can be combined into one, so can the `enteredPassword` & `passwordIsValid`. The transition will look like this:

```jsx
import React, { useState, useEffect, useReducer } from 'react';

import Card from '../UI/Card/Card';
import classes from './Login.module.css';
import Button from '../UI/Button/Button';

const emailReducer = (state, action) => {
  if (action.type === 'USER_INPUT') {
    return { value: action.val, isValid: action.val.includes('@') };
  }
  if (action.type === 'INPUT_BLUR') {
    return { value: state.value, isValid: state.value.includes('@') };
  }
  return { value: '', isValid: false };
};

const passwordReducer = (state, action) => {
  if (action.type === 'USER_INPUT') {
    return { value: action.val, isValid: action.val.trim().length > 6 };
  }
  if (action.type === 'INPUT_BLUR') {
    return { value: state.value, isValid: state.value.trim().length > 6 };
  }
};
const Login = (props) => {
  // const [enteredEmail, setEnteredEmail] = useState('');
  // const [emailIsValid, setEmailIsValid] = useState();
  // const [enteredPassword, setEnteredPassword] = useState('');
  // const [passwordIsValid, setPasswordIsValid] = useState();
  const [formIsValid, setFormIsValid] = useState(false);

  const [emailState, dispatchEmail] = useReducer(emailReducer, {
    value: '',
    isValid: undefined,
  });

  const [passwordState, dispatchPassword] = useReducer(passwordReducer, {
    value: '',
    isValid: undefined,
  });

  useEffect(() => {
    const timer = setTimeout(() => {
      // console.log('Effect')
      setFormIsValid(emailState.isValid && passwordState.isValid);
    }, 500);

    return () => {
      // console.log('CLEANUP');
      clearTimeout(timer);
    };
  }, [emailState, passwordState]);
  const emailChangeHandler = (event) => {
    dispatchEmail({ type: 'USER_INPUT', val: event.target.value });
  };

  const passwordChangeHandler = (event) => {
    dispatchPassword({ type: 'USER_INPUT', val: event.target.value });
  };

  const validateEmailHandler = () => {
    dispatchEmail({ type: 'INPUT_BLUR' });
  };

  const validatePasswordHandler = () => {
    dispatchPassword({ type: 'INPUT_BLUR' });
  };

  const submitHandler = (event) => {
    event.preventDefault();
    props.onLogin(emailState.value, passwordState.value);
  };

  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <div
          className={`${classes.control} ${
            emailState.isValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor='email'>E-Mail</label>
          <input
            type='email'
            id='email'
            value={emailState.value}
            onChange={emailChangeHandler}
            onBlur={validateEmailHandler}
          />
        </div>
        <div
          className={`${classes.control} ${
            passwordState.isValid === false ? classes.invalid : ''
          }`}
        >
          <label htmlFor='password'>Password</label>
          <input
            type='password'
            id='password'
            value={passwordState.value}
            onChange={passwordChangeHandler}
            onBlur={validatePasswordHandler}
          />
        </div>
        <div className={classes.actions}>
          <Button
            type='submit'
            className={classes.btn}
            disabled={!formIsValid}
          >
            Login
          </Button>
        </div>
      </form>
    </Card>
  );
};

export default Login;

```
Having Successfully combined these states, the above code is really not efficient because now, the `useEffect` function will run too often because it'll run whenever the `emailState` or `passwordState` changes and that includes cases where maybe just the value changes, or when our input is already valid and we enter an extra character. We only need the Effect to be dependent on if the states are valid or not and to achieve that we can use `object de-structuring` which is the same as `array-de-structing` but with object.

```js
const { isValid } = emailState;
```
Here we take whatever has the key `isValid` and store it in `const isValid`. But since we have two objects with `isValid` prop we'll have a conflict so we can assign an alias to de-extract the value, like this:
```js
const { isValid: emailIsValid } = emailState;
const { isValid: passwordIsValid } = passwordState;
```

Now our constants are now `emailIsValid` & `passwordIsValid` using **alias assignment**. Now in our code we can use `emailIsValid` & `passwordIsValid` as dependencies rather than the whole state.

```jsx
const { isValid: emailIsValid } = emailState;
const { isValid: passwordIsValid } = passwordState;

useEffect(() => {
  const timer = setTimeout(() => {
    // console.log('Effect')
    setFormIsValid(emailIsValid && passwordIsValid);
  }, 500);

  return () => {
  // console.log('CLEANUP');
    clearTimeout(timer);
  };
}, [emailIsValid, passwordIsValid]);

```

We could also use `emailState.isValid` and `passwordState.isValid`:
```jsx
useEffect(() => {
  const timer = setTimeout(() => {
    // console.log('Effect')
    setFormIsValid(emailState.isValid && passwordState.isValid);
  }, 500);

  return () => {
  // console.log('CLEANUP');
    clearTimeout(timer);
  };
}, [emailState.isValid, passwordState.isValid]);
```
but this can look scary and sometimes unreadable.

### useState() vs useReducer()
Generally, we'll know when we need useReducer() i.e. when useState becomes cumbersome or we're getting alot of bugs/unintended behaviors.

| useState() | useReducer() |
|------------|--------------|
|The main sate management 'tool'| Great if you need 'more power' |
| Great for independent pieces of state/data | Should be considered if you have related pieces of state/data |
| Great if state updates are easy and limited to a few kinds of updates | Can be helpful if you have more complex state updates | 

### Context
When we pass props through multiple components and those component does not necessarilly need the props, we are just passin the data through them, also when we lift state-up to a parallel component through multiple states, this can cause confussion in a very large scale application, that's where **Context API** comes in, we just need to trigger an action and the need props will be send directly to the component that needs it.

To use react context, we'll create a new folder in our components folder (maybe `context` or `store`) and we'll add a new file called `auth-context.js`:
```js
import React from 'react';

const AuthContext = React.createContext({
  isLoggedIn: false
});

export default AuthContext;
```
Now to use this context, we have to provide it and also consume it. We provide it where the data is needed by wrapping it around the components that need it or conatains a component that needs and consume it where we are using it. From our [useEffect example](#effect--side-effect) for the login page:
```jsx
import React from 'react';

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  const storedInfo = localStorage.getItem('isLoggedIn')

  if (storedInfo === '1') {
    setIsLoggedIn(true);
  }

  const loginHandler = (email, password) => {
    localStorage.setItem('isLoggedIn', '1');
    setIsLoggedIn(true);
  };

  const logoutHandler = () => {
    localStorage.removeItem('isLoggedIn');
    setIsLoggedIn(false);
  };

  return (
    <React.Fragment>
      <MainHeader isAuthenticated={isLoggedIn} onLogout={logoutHandler} />
      <main>
        {!isLoggedIn && <Login onLogin={loginHandler} />}
        {isLoggedIn && <Home onLogout={logoutHandler} />}
      </main>
    </React.Fragment>
  );
}

export default App;
```
We are passing the `isAuthenticated` prop to another component which then pass it to another component, we also need it for both the `home` and `login` components, so we'll wrap all of it with `auth-context` as a provider:

```jsx
import React from 'react';
import AuthContext from './store/auth

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  // .... some other code
  return (
    <React.Fragment>

      <MainHeader isAuthenticated={isLoggedIn} onLogout={logoutHandler} />
      <main>
        {!isLoggedIn && <Login onLogin={loginHandler} />}
        {isLoggedIn && <Home onLogout={logoutHandler} />}
      </main>
    </React.Fragment>
  );
}

export default App;
```

## Rendering

How does react render?

When you start your react app, we have the Real DOM...
React creates a Virtual DOM for changes purposes, and then a copy of that virtual DOM.

When a change is made to the DOM, the change is made to the copy of the virtual DOM, the Real DOM doesn't change until the virtual DOM and its copy are compared without issues.

# Projects

| Project                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Repo Link | Live Link |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- | --------- |
| Monsters Rolodex        | This is my first project using ReactJS, It's basically a page that displays a bunch of cards and filters them while you search.                                                                                                                                                                                                                                                                                                                                                                | Link      | Link      |
| Pure React              | This is my second encounter with ReactJS while learning and documenting this, although I have seen this concept before (used in Django to write react). This is basically REACT under the hood, without the whole packages and modules, you just import what you need from the cloud and write your app and stuff                                                                                                                                                                            | Link      | Link      |
| React Complete Guide    | This is the first section of the tutorial from Zero to mastery(insert link), it basically covers the fundamentals of react, It is an expense tracker app, users input their expenses and they can filter by year and they can see how much each expense is compared to the maximum expense in a year via chart bar.                                                                                                                                                                          | Link      | Link      |
| Styling React Component | In this project I learnt different ways to style a component so that there won't be a styling clash in the project, I learnt how to use styled-component (a node package) and CSS modules, I also learnt how to style dynamically if certain logic applies using the ternary operator and even if-statements in different scenarios, You can read about them [here](#styled-components), [here](#css-modules), [here](#dynamic-styling) and [here](#rendering-conditional-contents) respectively | Link      | Link      |
| Add User App            | This is my first 'no-help' react project, where a user can input their name and age, if they input an incorrect age like a negative number or they submit an empty form or they leave an input field blank while submitting, they get their respective error messages in a dialogue box. If they successfully add their name and age, it will be displayed in the bottom section of the app.                                                                                                   | Link      | Link      |
| Effects, Reducers & Context | This 


