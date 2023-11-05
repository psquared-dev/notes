- Nullish Coalescing operator - This relatively-new addition to the language is similar to the Logical OR operator (||), except instead of relying on truthy/falsy values, it relies on "nullish" values (there are only 2 nullish values, null and undefined). This means it's safer to use when you treat falsy values like 0 as valid.

---

https://www.joshwcomeau.com/operator-lookup?match=nullish-coalescing

- in earlier versions of react prior to 18, we needed to have the `import React from "react"` statement explicitly, otherise the React would throw an error. The reason is JSX. Remember JSX is just a call to `React.createElement()` method of react.

---

- Props are like arguments to a function: they allow us to pass data to our components, so that the components can include customizations based on the data

---

- When we pass something between the open and close tags, React will automatically supply that value to us under children prop. If we wanted to, we could pass children in the "traditional" way. It's clunky, but the outcome is the same:

```html
// This element:
<div children="Hello world!" />

// …is equivalent to this one:
<div>Hello world!</div>
```

Thus, fundamentally, the children prop is the same as any other prop.

---

- what's the need of fragments? consider the following retun statement from a React component:

```html
return (
<h1>Welcome to my homepage!</h1>
<p>Don't forget to sign the guestbook!</p>
);
```

React would transform the above statement into the following:

return (
React.createElement("h1", null, ...)
React.createElement("p", null, ...)
)

Here we are returning the value of two function calls. As per the rules of JS this is invalid syntax. Here comes the need of Fragments.

---

Mapping over data

You can render an array inside the JSX, React will unpack it for you. This is the reason why we're able to use array methods `map()` to print the list.

---

Key and ref are not passed as props to React components

---

React elements (React.createElement) are JavaScript objects that describe a thing that React needs to create. In this case, the element describes a ContactCard component that needs to be rendered.

When using fragments, sometimes its required to switch to the long-form React.Fragment, so that we can apply the key:

```jsx
<React.Fragment key={item.id}>
  <p>{item.content}</p>
  <button>Cancel</button>
</React.Fragment>
```

Note: Keys don't need to be unique

Many developers believe that keys has to be globally unique, across the entire application, but this is a misconception. Keys only have to be unique within their array.

### Using index as key

Indexes are indeed unique within the context of the array, but there's an issue: they don't really uniquely identify the item itself, they only identify the item's position in the array. This can lead to performance problems, as well as strange quirks.

---

## Why regular if statements doesn't work inside the expression slot in JSX

With the curly brackets, we can add JavaScript expressions within our JSX. Unfortunately, though, we can't add JavaScript statements.

Consider the following example:

```jsx
function Friend({ name, isOnline }) {
  return (
    <li className="friend">
      {if (isOnline) {
        <div className="green-dot" />
      }}

      {name}
    </li>
  );
}
```

this would compile down to following JavaScript:

```jsx
function Friend({ name, isOnline }) {
  return React.createElement(
    'li',
    { className: 'friend' },
    if (isOnline) {
      React.createElement('div', { className: 'green-dot' });
    },
    name
  );
}
```

Issue here is that the `if` statement doesn't evaluate to a value that's why it can't be used inside the expression slot.

---

## React Ignores undefined values

Consider the following code:

```jsx
function Friend({ name, isOnline }) {
  let prefix;

  if (isOnline) {
    prefix = <div className="green-dot" />;
  }

  return (
    <li className="friend">
      {prefix}
      {name}
    </li>
  );
}
```

In the code above, prefix will either be assigned to a React element, or it won't be defined at all. This works because React ignores undefined values.

The same thing is applicable for attributes. When React sees that an attribute is being set to undefined, it omits that attribute entirely from the DOM node. Rather than give it an empty value like className="", it acts as though we haven't even tried to set a value.

This is true for some other falsy values as well, like null and false.

---

The && operator doesn't return true or false. It returns either the left-hand side or the right-hand side. So, when our list is empty, this expression evaluates to 0.

React will ignore most falsy values like false or null, but it won't ignore the number zero.

Consider the following code:

```jsx
function Banner({ ticketsRemaining }) {
  return <h2>Number of JIRA tickets left: {ticketsRemaining}.</h2>;
}
```

If ticketsRemaining is equal to 0, we want to show the number 0, not an empty space!

To avoid having random 0 characters sprinkled around your application, I suggest following a “golden rule”: make sure that the left-hand side of && always evaluates to a boolean value, either true or false.

https://courses.joshwcomeau.com/joy-of-react/01-fundamentals/07.02-and-and

---

CSS Modules

https://courses.joshwcomeau.com/joy-of-react/01-fundamentals/09.01-intro-to-css-modules

---

-------------------------------- Module - 2 ---------------------------------------

```jsx
function App() {
  function doSomething() {
    // Stuff here
  }

  return <button onClick={doSomething}>Click me!</button>;
}
```

This is the recommended way to handle events in React. While we do sometimes have to use addEventListener for window-level events (covered in Module 3), we should try and use the “on X” props like onClick and onChange whenever possible.

Here is the reason:

Automatic cleanup. Whenever we add an event listener, we're also supposed to remove it when we're done, with removeEventListener. If we forget to do this, we'll introduce a memory leak?. React automatically removes listeners for us when we use “on X” handler functions.

---

Passing argumens to event handler can also be done using the bind method:

```jsx
// ✅ Valid:
<button onClick={setTheme.bind(null, "dark")}>Toggle theme</button>
```

---

useState is a hook. A hook is a special type of function that allows us to "hook into" React internals. We'll learn much more about hooks later in this course.

---

We can also supply a function. React will call this function on the very first render to calculate the initial value:

```js
const [count, setCount] = React.useState(() => {
  return 1 + 1;
});

console.log(count); // 2
```

This is sometimes called an initializer function. It can occasionally be useful if we need to do an expensive operation to calculate the initial value. For example, reading from Local Storage:

The benefit here is that we're only doing the expensive work (reading from Local Storage) once, on the initial render, rather than doing it on every single render.

---

So, when we talk about “re-rendering” a component, we aren't necessarily saying that anything will change in the DOM! We're saying that React is going to check if anything's changed. If React spots a difference between snapshots, it'll need to update the DOM, but it will be a precisely-targeted minimal change.

When React does change a part of the DOM, the browser will need to re-paint. A re-paint is when the pixels on the screen are re-drawn because a part of the DOM was mutated. This is done natively by the browser when the DOM is edited with JavaScript (whether by React, Angular, jQuery, vanilla JS, anything).

A re-render is a React process where it figures out what needs to change (AKA. “reconciliation”, the spot-the-differences game).

---

```jsx
function App() {
  const [count, setCount] = React.useState(0);

  return (
    <>
      <p>You've clicked {count} times.</p>
      <button
        onClick={() => {
          setCount(count + 1);

          console.log(count);
        }}
      >
        Click me!
      </button>
    </>
  );
}
```

What value would you expect to see in the developer console when the user clicks the button for the first time?

When we create our state variable, we initialize it to 0. Then, when we click the button, we increment it by 1, to 1. So shouldn't it log 1, and not 0?

Here's the catch: state setters aren't immediate.

When we call setCount, we tell React that we'd like to request a change to a state variable. React doesn't immediately drop everything; it waits until the current operation is completed (processing the click), and then it updates the value and triggers a re-render.

For now, the important thing to know is that updating a state variable is asynchronous. It affects what the state will be for the next render. It's a scheduled update.

---

Controlled vs. Uncontrolled inputs

When we set the value attribute on a form input, we tell React that it should be a controlled input. The word “controlled” has a specific definition in React; it means that React is managing the input.

By contrast, if we don't set value, the input is an uncontrolled input. This means that React doesn't do any management.

There's a golden rule here: An input should always either be controlled or uncontrolled. React doesn't like when we flip an element from one to the other

Consider the following example:

```jsx
import React from "react";

function SignupForm() {
  // No default value:
  const [username, setUsername] = React.useState();

  return (
    <form>
      <label htmlFor="username">Select a username:</label>
      <input
        type="text"
        id="username"
        value={username}
        onChange={(event) => {
          setUsername(event.target.value);
        }}
      />
    </form>
  );
}

export default SignupForm;
```

Try typing in the text input, and then switch to the “Console” tab. You should see a warning that begins like this:

Warning: A component is changing an uncontrolled input to be controlled.

This is weird, right? This input is controlled! We're setting value={username} from the very first render!

Here's the problem: username is undefined at first, since there is no default value in the state hook. Here's a simplified version of what we're doing:

When we set value to undefined, it's the same as not setting it at all. React will treat the input as an uncontrolled input.

When the user starts typing in the input, the onChange event updates the value of username from undefined to a string. And so, React flips the element to a controlled input, and raises the warning.

Here's how to solve the problem: We always want to make sure we're passing a defined value. We can do this by initializing username to an empty string:

Data binding not required? (info)

A growing trend in the React ecosystem is to wire up forms so that we don't need to use controlled inputs at all. Instead, we collect all the data we need on submit, using built-in DOM helpers like FormData:

```jsx
unction SignupForm() {
  function handleSubmit(event) {
    const formData = new FormData(event.target);
    const { username } = Object.fromEntries(formData);

    // Do something with `username`, like send it
    // to the server.
  }

  return (
    <form>
      <label htmlFor="username">
        Select a username:
      </label>

      <input
        type="text"
        id="username"
        name="username"
      />
    </form>
  );
}
```

This is a pretty slick approach, but there's a problem: it won't work in a bunch of different situations.

Unless we store that value in a state variable, we won't be able to render it in our UI. And if we need to store it in state, the easiest thing is to use a controlled input.

---

There's a mistake I've seen lots of React developers make. This lesson is a cautionary tale about it.

Let's suppose we're building a search form:

```jsx
import SearchForm from "./SearchForm";

function App() {
  // This function is a placeholder.
  function runSearch(searchTerm) {
    window.alert(`Searched for: ${searchTerm}`);
  }

  return <SearchForm runSearch={runSearch} />;
}

export default App;
```

n this example, runSearch is the function we want to call when the user clicks/taps the "Search!" button. In Module 3, we'll learn how to make network requests, but for now, it's a stub?.

Here's the question: how should I use this function?

A lot of developers instinctively solve for this by adding an onClick handler to the submit button:

```jsx
<button onClick={() => runSearch(searchTerm)}>Search!</button>
```

There are a number of problems with this approach. For example, what if the user tries to search by pressing "Enter" after typing in the text input?

Well… I suppose we could tackle that with an onKeyDown event listener?

```jsx
<input
  type="text"
  value={searchTerm}
  onChange={(event) => {
    setSearchTerm(event.target.value);
  }}
  onKeyDown={(event) => {
    if (event.key === "Enter") {
      runSearch(searchTerm);
    }
  }}
/>
```

We're going down a bad road here. We're re-implementing stuff that the browser already knows how to do!

To solve this problem, along with so many others, we should wrap our form controls in a <form> tag.

Then, instead of listening for clicks and keys, we can listen for the form submit event.

The form submit event will be called automatically when the user clicks the button, or presses "Enter" whenever the input or button is focused. When that event fires, we'll run our search.

Instead of trying to re-create a bunch of standard web platform stuff, we should use the platform and let it solve these sorts of problems for us!

By using a form submit event, we get to use client-side validation:

```jsx
<input type="password" required={true} minLength={8} />
```

---

Other Form Controls

If you've ever had to work with these form controls outside of React, you've likely been surprised/frustrated by how dissimilar they are from one another.

For example, textareas define the current value as children, rather than using a value prop:

As another example, select tags use a selected prop on one of the <option> children to signify the selected value.

Here's the good news: React has tweaked many of these form controls so that they all behave much more similarly. There's a lot less chaos with form controls in React.

Essentially, all form controls follow the same pattern:

1. The current value is locked using either value (for most inputs) or checked (for checkboxes and radio buttons).
2. We respond to changes with the onChange event listener.

---

Props are the inputs to our components, like arguments passed to a function. Props are the tunnels that allow data to flow through our application.

---

Spread Operator

const a = [1,2,3,4,5]

const b = [a[0], a[1], a[2], a[3], a[4]] // clone the array a

the above line can be shortened to:

const b = [...a]

---

Dynamic Key Generation

Don't use Math.random() to generate keys. See this:

https://courses.joshwcomeau.com/joy-of-react/02-state/07-key-generation
https://courses.joshwcomeau.com/joy-of-react/02-state/07.01-key-gotchas

Technically, it is safe to use the array index as the key if the order stays 100% consistent. If the items never change position, there won't be any issues.

To sum it up: Generate a truly unique value for each element in the array. It's a bit more work upfront, but it can avoid a lot of confusion down the road.

---

Component Instances:

https://courses.joshwcomeau.com/joy-of-react/02-state/09-component-instances
