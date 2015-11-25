# react-redux-notes
Study notes of React-Redux from DanAbramov's Talk

## 1. The Single Immutable State Tree
The first principle of Redux is, the entire application's state is managed through a Single JS Object i.e, **'Single Immutable State Tree'**.

Everything that changes within the application, including the data & the UI State are contained in single JS Object called 'State Tree'.

In more complex applications, more state to keep track of. You can track back the state changes from this State tree.

## 2. Describing State changes with Actions
The state-tree is read-only.

Whenever you want to change the state, you need to dispatch an action.

An action is a plain js object which defines the change.

Just like the State is the minimial representation of data in the app, The Action is the minimal representation of the change to the data.

The structure of Action object is up to you, the only requirement is that it has a

`type` property, which is not undefined.

Its suggested that to use "String" as it's serializable.

Different type of apps have Different type of actions. We don't need additional information for passing the action.

## 3. Pure & Impure Function
Its important to understand the pure & impure functions.

### Pure Functions
Pure Functions are the functions whose return values are solely dependent on the parameters passed on to the functions. Pure Functions doesn't have any observable side effects such as network/database calls.

You can be confident that if you call a pure function, for the  same arguments, your are gonna get the same return values always. Results are predictable.

```
function square(x) {
  return x * x;
}
```

Pure functions doesn't modify the values to passed to them. In the following example, the items array is not modified, instead it returns a new Array, by using items.map();

```
function squareAll(items) {
  return items.map(square);
}
```

### Impure Functions
On the opposite,  Impure functions, they may have side effects; they may call db, make network requests, they may override the values to passed to them.

```
function square(x) {
  updateXinDB(x);
  return x * x;
}

function squareAll(items) {
  for(let i=0; i<items.length; i++ ){
    items[i] = square(items[i]);
  }
}
```

## 4. The Reducer Function
The UI/View layer is more predictable, when it is just described as pure functions of the application state. This approach was pioneered by React.

Redux complements this approach with another idea.

The state mutation in your app should be  described as pure function.

That takes `PreviousState` , `ActionBeingDispatched` and return the `NextState` of your application.

In any redux application, there is one particular function that takes the  state of the whole application and the action being dispatched and return the next state of the whole application.

It is important that it doesn't modify the state to given to it. It has to be pure and should only return the next state.(new object)

This function is called **'Reducer'** Function.

## 5. Writing a counter Reducer with Tests
We are going to write a Simple Counter example using Reducer.

```
function counter(state, action) {
  return state;
}
```

Lets write some test cases using `expect` framework

```
  expect(
    counter(0, {type:'INCREMENT'})
  ).toEqual(1);

  expect(
      counter(1, {type:'INCREMENT'})
  ).toEqual(2);

  expect(
      counter(2, {type:'DECREMENT'})
  ).toEqual(1);

  expect(
        counter(1, {type:'DECREMENT'})
  ).toEqual(0);

  console.log('Tests Passed');
```

These test gonna fail, as we've not implemented the Reducer completely.

Lets do that now. Based on the ``action.type`` we need to change the state.

```
function counter(state,action) {
  if(action.type==='INCREMENT') {
    return state + 1;
  } else if(action.type==='DECREMENT') {
    return state - 1;
  }
}
```

Now the tests are passing. However if we pass an action which is not understood by the reducer, we need to maintain the current state.

```
expect(
      counter(1, {type:'SOMETHING_ELSE'})
).toEqual(0);

```

Let us change the reducer to handle this.

```
function counter(state,action) {
  if(action.type==='INCREMENT') {
    return state + 1;
  } else if(action.type==='DECREMENT') {
    return state - 1;
  } else {
    return state;
  }
}
```

The initial state of the application can be handled as follows

```
expect(
      counter(undefined, {})
).toEqual(0);

```
This will fail the test cases.
```
function counter(state,action) {
if(typeof state ==== 'undefined') {
  return 0;
}

  if(action.type==='INCREMENT') {
    return state + 1;
  } else if(action.type==='DECREMENT') {
    return state - 1;
  } else {
    return state;
  }
}
```

Let us change the schematic and use ES6 syntax.

```
const counter = (state = 0,action) => {

switch(action.type) {
  case 'INCREMENT' :
    return state + 1;
  case 'DECREMENT':
    return state - 1;
  default:
  return state;
}
}
```

## 6.Store Methods - getState(), dispatch() and subscribe()
