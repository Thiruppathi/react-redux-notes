# react-redux-notes
Study notes of React-Redux.

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

Lets do that now. Based on the `action.type` we need to change the state.

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
The Store binds together the 3 principles of Redux.
- It holds the current application state object.
- It lets you dispatch actions.
- When they are created, you need to specify the reducer to tell how the state is updated by the Actions.

The Store has 3 important methods. react-design-diagram
1. store.getState();
2. store.dispatch({type:'INCREMENT'});
3. store.subscribe() - It lets you register a callback, that redux Store will call it whenever a Action is dispatched.
4. So that you can update your UI of your application.

[JSBin of the Demo](http://jsbin.com/nujole/2/edit?html,js,console,output)

## 7. Implementing Store from Scratch
In the previous step, we looked at how to implement a simple counter example by using createStore() provided by Redux.

Now we are going to re-implement createStore() provide by Redux from scratch.

[JS Bin of Demo](http://jsbin.com/cewote/edit?html,js,output) Instead of using Redux createStore() as follows,

```
const {createStore} = Redux;
```

We gonna create the createStore as follows.

```
const createStore = (reducer) => {
  let state;
  let listeners = [];

  const getState = () => state;

  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener());
  };

  const subscribe = (listener) => {
    listeners.push(listener);
    return () => {
      listeners = listeners.filter(l => l !== listener);
    };
  };

  dispatch({});

  return {getState, dispatch, subscribe};

};
```

## 8. React Counter example
Lets use React components to render the Counter value.

[JS Bin Demo](http://jsbin.com/razemo/edit?html,js,console,output)

## 9. Avoiding Array Mutation using concat, slice & ...spread
[JS Bin Demo](http://jsbin.com/zucogi)
<script src="https://gist.github.com/anonymous/2b9775562a5c4d01abd7.js"></script>

## 10. Avoid Object Mutation with Object.assign() and ...spread
[JS Bin Demo](http://jsbin.com/zoveqo/3/edit)

```

const toggleTodo = (todo) => {
  return {
    ...todo,
    completed: !todo.completed
  };
};

/*
const toggleTodo = (todo) => {
  return Object.assign({}, todo, {
    completed: !todo.completed
  });
};
*/

const testToggleTodo = () => {

  const todoBefore = {
    id:0,
    text: 'Learn Redux',
    completed: false
  };

    const todoAfter = {
    id:0,
    text: 'Learn Redux',
    completed: true
  };

  deepFreeze(todoBefore);
  expect(toggleTodo(todoBefore)).toEqual(todoAfter);

};

testToggleTodo();

console.log('All tests Passed.');
```

You can use `Object.assign()` or ...spread operator to mutate Object.

When using `Object.assign()` use a Polyfill to support evergreen browsers.

## 11. Writing a ToDo List Reducer ( Adding a ToDo)
[JS Bin Demo](http://jsbin.com/tenizo/edit?js,console)

```
const todos = (state=[],action) => {
  switch(action.type) {
    case 'ADD_TODO':
      return[
        ...state,
        {
          id: action.id,
          text: action.text,
          completed: false
        }
      ];
    default:
      return state;
  }
};

const testAddTodo = () => {
  const stateBefore = [];
  const action = {
    type: 'ADD_TODO',
    id: 0,
    text: 'Learn Redux'
  };

  const stateAfter = [
    {
      id:0,
      text:'Learn Redux',
      completed: false
    }
  ];

  deepFreeze(stateBefore);
    deepFreeze(action);

  expect(
    todos(stateBefore, action)
  ).toEqual(stateAfter);
};

testAddTodo();
console.log('All tests passed.');
```

## 12. Writing a Todo List Reducer - Toggling a Todo
[JS Bin Demo](http://jsbin.com/moyepe/edit?js,console)

```
const todos = (state=[],action) => {
  switch(action.type) {
    case 'ADD_TODO':
      return[
        ...state,
        {
          id: action.id,
          text: action.text,
          completed: false
        }
      ];
        case 'TOGGLE_TODO':
            return state.map(todo => {
                if(todo.id !== action.id) {
                    return todo;
                }

                return {...todo, completed: !todo.completed };
            });
    default:
      return state;
  }
};



const testAddTodo = () => {
  const stateBefore = [];
  const action = {
    type: 'ADD_TODO',
    id: 0,
    text: 'Learn Redux'
  };

  const stateAfter = [
    {
      id:0,
      text:'Learn Redux',
      completed: false
    }
  ];

  deepFreeze(stateBefore);
    deepFreeze(action);

  expect(
    todos(stateBefore, action)
  ).toEqual(stateAfter);
};

const testToggleTodo = () => {
  const stateBefore = [
    {
      id:0,
      text:'Learn Redux',
      completed: false
    },
        {
      id:1,
      text:'Learn NodeJS',
      completed: false
    }
  ];  
  const action = {
    type: 'TOGGLE_TODO',
    id: 1
  };
    const stateAfter = [
    {
      id:0,
      text:'Learn Redux',
      completed: false
    },
        {
      id:1,
      text:'Learn NodeJS',
      completed: true
    }
  ];  


  deepFreeze(stateBefore);
    deepFreeze(action);

  expect(
    todos(stateBefore, action)
  ).toEqual(stateAfter);
};

testAddTodo();
testToggleTodo();
console.log('All tests passed.')
```

## 13. Reducer Composition with Arrays
[JS Bin Demo](http://jsbin.com/mumaya/edit?js,console)

```
const todo = (state, action) => {
    switch(action.type) {
        case 'ADD_TODO':
            return {
                id: action.id,
                text: action.text,
                completed: false
            };
        case 'TOGGLE_TODO':
            if(state.id !== action.id) {
                return state;
            }

            return {
                ...state,
                completed: !state.completed
            };
        default:
            return state;
    }
};


const todos = (state=[],action) => {
  switch(action.type) {
    case 'ADD_TODO':
      return[
        ...state,
        todo(undefined, action)
      ];
        case 'TOGGLE_TODO':
            return state.map(t => todo(t,action));
    default:
      return state;
  }
};
```

## 14. Reducer Composition with Objects
[JS Bin Demo](http://jsbin.com/wivutu/edit?js,console)

```
const todo = (state, action) => {
    switch(action.type) {
        case 'ADD_TODO':
            return {
                id: action.id,
                text: action.text,
                completed: false
            };
        case 'TOGGLE_TODO':
            if(state.id !== action.id) {
                return state;
            }

            return {
                ...state,
                completed: !state.completed
            };
        default:
            return state;
    }
};


const todos = (state=[],action) => {
  switch(action.type) {
    case 'ADD_TODO':
      return[
        ...state,
        todo(undefined, action)
      ];
        case 'TOGGLE_TODO':
            return state.map(t => todo(t,action));
    default:
      return state;
  }
};


const visibilityFilter = (
  state = 'SHOW_ALL',
  action
  ) => {
    switch(action.type) {
        case 'SET_VISIBILITY_FILTER':
            return action.filter;
        default:
            return state;
    }
};


const todoApp = (state ={}, action) => {
    return {
        todos: todo(state.todos, action),
        visibilityFilter: visibilityFilter(state.visibilityFilter, action)
    };
};


const {createStore} = Redux;
const store = createStore(todoApp);
```

## 15. Reducer Composition with combineReducers()
[JS Bin Demo](http://jsbin.com/puqalo/edit?js,console)

```
const todoApp = (state ={}, action) => {
    return {
        todos: todo(state.todos, action),
        visibilityFilter: visibilityFilter(state.visibilityFilter, action)
    };
};
```

This pattern is so common in most of the Redux-Apps.

Thats why Redux provides a function called `{combineReducers}`

```
const {combineReducers} = Redux;

const todoApp = combineReducers ({
  todos: todos,
  visibilityFilter : visibilityFilter
  });
```

The Keys of the object that configured in the combineReducers denotes the fields  of the state object.

The Values of the object that configured in the combineReducers denotes the reducers it should call to update the corresponding state fields.

Let's establish a useful convention.  I'll always name my Reducers after the state keys they manage.

Thanks to ES6 Object literal shorthand notation.

```
const {combineReducers} = Redux;

const todoApp = combineReducers ({ todos,  visibilityFilter  });
```

## 16. Implementing combineReducers() from Scratch
[JS Bin Demo](http://jsbin.com/tazuzi/edit?js,console)

To gain deeper understanding of combineReducers works, let us implement it from Scratch.

```
const combineReducers = (reducers) => {
  return (state = {}, action) => {
    return Object.keys(reducers).reduce(
      (nextState, key) => {
        nextState[key] = reducers[key](
          state[key],
          action
        );
        return nextState;
      }, {}
    );
  };
};
```

## 17. React ToDo List Example (Adding a ToDo)
[JS Bin Demo](http://jsbin.com/nimeqe/edit?html,js,output)

```

const { Component } = React;

let nextTodoId = 0;

class TodoApp extends Component {
    render() {
        return (
          <div>
            <input ref={ node => {
            this.input = node;
            }} />
              <button onClick={()=> {
                store.dispatch({
                        type: 'ADD_TODO',
                        text: this.input.value,
                        id: nextTodoId++
                    });
            this.input.value = '';
        }}>
      Add Todo
        </button>
        <ul>
            {this.props.todos.map(todo =>
             <li key = {todo.id}>
              {todo.text}
            </li>
             )}
        </ul>
            </div>
        );
    }
}

const render = () => {
    ReactDOM.render(
    <TodoApp
        todos={store.getState().todos}
  />,
    document.getElementById('root')
    );
};

store.subscribe(render);
render();
```

## 18. React ToDo List Example (Toggling a ToDo)
[JS Bin Demo](http://jsbin.com/kozaba/edit?html,js,output)

```
<ul>
  {this.props.todos.map(todo =>
   <li  key = {todo.id}
        onClick={()=>{
                 store.dispatch({
                 type: 'TOGGLE_TODO',
                 id: todo.id
                });
        }}
        style = {{
          textDecoration: todo.completed ? 'line-through' : 'none'
                }}>
  {todo.text}
  </li>
   )}
</ul>
```

## 19. React ToDo List Example (Filtering a ToDo)

[JS Bin Demo](http://jsbin.com/zedafi/edit?html,js,output)

```
const FilterLink = ({
  filter,
  currentFilter,
  children
}) => {
  if (filter === currentFilter) {
    return <span > {
      children
    } < /span>;
  }
  return ( < a href = '#'
    onClick = {
      e => {
        e.preventDefault();
        store.dispatch({
          type: 'SET_VISIBILITY_FILTER',
          filter
        });
      }
    } > {
      children
    } < /a>
  );

};
```

```
const getVisibleTodos = (
  todos, filter
) => {
  switch (filter) {
    case 'SHOW_ALL':
      return todos;
    case 'SHOW_COMPLETED':
      return todos.filter(t => t.completed);
    case 'SHOW_ACTIVE':
      return todos.filter(t => !t.completed);
  }
};
```

```
< p >
  Show: {
    ' '
  } < FilterLink filter = 'SHOW_ALL'
currentFilter = {
  visibilityFilter
} > All < /FilterLink> {
' '
} < FilterLink filter = 'SHOW_ACTIVE'
currentFilter = {
  visibilityFilter
} > Active < /FilterLink> {
' '
} < FilterLink filter = 'SHOW_COMPLETED'
currentFilter = {
  visibilityFilter
} > Completed < /FilterLink> < /p >

```


## 20. Extracting Presentational components (ToDo, ToDo List)

[JS Bin Demo](http://jsbin.com/nefefi/edit?js,output)

Let's Extract the ToDo Component.

```
const Todo = ({ onClick,completed, text }) => (
	<li
	  onClick = {onClick}
    style = {{textDecoration: completed ? 'line-through' : 'none'}}>
	    {text}
	</li>
);
```

Now, Extract the TodoList Component.

```
const TodoList = ({	todos,	onTodoClick }) => (
    <ul>
      {todos.map(todo =>
				  <Todo
				    key={todo.id}
	          {...todo}
	          onClick={() => onTodoClick(todo.id)}
          />
      )}
    </ul>
);

```

Now, in the render method, repalce the `<ul>` with our extracted component.

```
<TodoList
  todos = {visibleTodos}
   onTodoClick = {id =>
      store.dispatch({
        type: 'TOGGLE_TODO',
        id
      })
  } />
```


## 21. Extracting Presentational components (Add ToDo, Footer, Filter Link)

[JS Bin Demo](http://jsbin.com/tedoqi/edit?html,js,output)

Add ToDo

```
const AddTodo = ({onAddClick}) => {
	let input;

	return (
      <div>
        <input ref = { node => {input = node;}}/>
        <button onClick = { () => {
							onAddClick(input.value);
							input.value = '';
					}}>
						Add Todo
        </button>
			</div>
			);
};
```

Footer

```
const Footer = ({visibilityFilter, onFilterClick}) => (
				<p>
           Show :
             {' '}
             <FilterLink filter = 'SHOW_ALL'  currentFilter = {visibilityFilter} onClick={onFilterClick}>
							 All
						 </FilterLink>
             {' ,  '}
             <FilterLink filter = 'SHOW_ACTIVE'  currentFilter = {visibilityFilter} onClick={onFilterClick}>
							 Active
						 </FilterLink>
             {' ,  '}
             <FilterLink filter = 'SHOW_COMPLETED'  currentFilter = {visibilityFilter} onClick={onFilterClick}>
							 Completed
						 </FilterLink>
        </p>
);
```

Filter Link

```
const FilterLink = ({ filter, currentFilter, children, onClick }) => {

	if (filter === currentFilter) {
    return <span> {children} </span>;
	}

	return (
		<a
        href = '#'
        onClick = { e => {
                    e.preventDefault();
                    onClick(filter)
                  }
        }>
      {children}
    </a>
  );
};
```

The final ToDoApp component

```
const TodoApp = ({todos, visibilityFilter}) => (
      <div>

			<AddTodo
			      onAddClick={text=>
			        store.dispatch({
			          type:'ADD_TODO',
			          id:nextTodoId++,
			          text
          		})
			      }
	    />

        <TodoList
          todos = {getVisibleTodos(todos, visibilityFilter)}
      		 onTodoClick = {id =>
              store.dispatch({
                type: 'TOGGLE_TODO',
                id
              })
          } />

					<Footer
					  visibilityFilter={visibilityFilter}
						onFilterClick={filter =>
						  store.dispatch({
									type: 'SET_VISIBILITY_FILTER',
									filter
									})
						}
					/>
      </div>
    );
```

Separation of Presentational Component, decouples the rendering from REDUX.

This has a downside of passing too many properties to the component, which can be resolved using **Container Components**.


## 22. Extracting Container components (Filter Link)

[JS Bin Demo](http://jsbin.com/woloqo/edit?html,js,output)

In previous step, **Footer** component passes down `visibilityFilter` and `onFilterClick` to the **FilterLink** component, even though **Footer** doesn't use this properties directly.

In this way, it breaks encapsulation where the **ParentComponent** needs to know too much about the **ChildComponent**.

Thats why we need to extract the **Container Components**, just like the way we extracted **Presentational Components**.

Let's start refactoring with the **Footer** component by removing the properties `visibilityFilter` and `onFilterClick`.

In from ToDoApp Component,
```
<Footer	/>
 ```

And from the Footer Component

```
const Footer = () => (
				<p>
           Show :
             {' '}
             <FilterLink filter = 'SHOW_ALL'>All</FilterLink>
             {' ,  '}
             <FilterLink filter = 'SHOW_ACTIVE'>Active</FilterLink>
             {' ,  '}
             <FilterLink filter = 'SHOW_COMPLETED'>Completed</FilterLink>
        </p>
);
```

This may seem like the same component before extracting to **Presentational Component**.

But what we are going to do is bit Different.


Let's have a look at **Filter** Component.
```
const FilterLink = ({ filter, currentFilter, children, onClick }) => {

	if (filter === currentFilter) {
    return <span> {children} </span>;
	}

	return (
		<a
        href = '#'
        onClick = { e => {
                    e.preventDefault();
                    onClick(filter)
                  }
        }>
      {children}
    </a>
  );
};
```

The FilterLink
 - doesn't specify the behaviour for Clicking on the link.
 - It also needs the current filter to tell whether its active or not to render.

Honestly, this is not a **Presentational Component**, because its not separable with its behaviour.

The only reasonable  reaction to clicking on it is to set visibilityFilter by dispatching an action.

This is why we should change it different **Presentational Component** as follows.

```
const Link = ({ active, children, onClick }) => {

	if (active) {
    return <span> {children} </span>;
	}

	return (
		<a
        href = '#'
        onClick = { e => {
		      e.preventDefault();
        	onClick();
        }}
		>
			{children}
    </a>
  );
};
```

The **Link** component doesn't know anything about filtering.

- It only accepts `active` property
- Handles click through `onClick()`

Let us create another component **FilterLink** as a container that uses **Link** to render it.
Its gonna render the **Link** from the current data from `store`.

Its going to
- read the props from the component props.
- read the state from redux's store state


```
class FilterLink extends Component {

	render() {
		const props = this.props;
		const state = store.getState();
	}
}

```
As a **Container Component** the **FilterLink** doesn't have its own mark up.
It delegates the rendering through the **Link Presentational Component**.

In this case it calculates it's `active` props, by comparing with the redux store state's `visibilityFilter`.

The `filter` props is the one we passed from the **Footer** component.
The `visibilityFilter` corresponds to the currently chosen `visibilityFilter`.

I these 2 filters matches, then we make the link to appear active.

```
<Link  
    active = { props.filter === state.visibilityFilter } >
</Link>
```

The Container Component also needs to define the  **behaviour**.

In this case, the **FilterLink** specifies when this particular link is clicked,
- dispatch an `action` with the type `SET_VISIBILITY_FILTER`
- take the `filter` value from the props

The filter link may contain children which is used as content of the **Link**

```
<Link
  active = { props.filter === state.visibilityFilter }
  onClick={() =>
    store.dispatch({
      type: 'SET_VISIBILITY_FILTER',
      filter: props.filter
    })
  }>
  {props.children}
</Link>
```

return this `Link` markup and the `FilterComponent` becomes like this.


```
class FilterLink extends Component {

 render() {
   const props = this.props;
   const state = store.getState();

   return (
     <Link
       active = { props.filter === state.visibilityFilter }
       onClick={() =>
         store.dispatch({
           type: 'SET_VISIBILITY_FILTER',
           filter: props.filter
         })
       }
     > {props.children}
     </Link>
   );
 }
}

```

There is a small problem with the implementation of **FilterLink**.
Inside the `render()` it reads the current state of the ReduxStore, but it is not subscribed to the Store.

So if the ParentComponent doesn't updated when the store is updated,
its going to render the child value.

Currently we re-render the whole application when the state changes, which is not efficient.

So in future, we will move subscription to the store to the LifeCycle methods of **Container Component**.

React provides a special `forceUpdate()` method on the component instances.

To force the re-rendering. And we are going to use it inside `store.subscribe()` ,
so that whenever the store's state changes we can forceUpdate the **Container Component**

```
componentDidMount() {
  store.subscribe(()=>
    this.forceUpdate()
  );
}
```

We did the `forceUpdate` inside `componentDidMount()`. We need to `unsubscribe` inside `componentWillUnMount()`

```
componentWillUnMount() {
  this.unsubscribe();
}

```

React doesn't provide `unsubscribe()` . Its a return value from `store.subscribe()`.

```
componentDidMount() {
  this.unsubscribe = store.subscribe(()=>
    this.forceUpdate()
  );
}

componentWillUnMount() {
  this.unsubscribe();
}
```

So our `FilterLink` becomes like this.

```
class FilterLink extends Component {

	componentDidMount() {
		this.unsubscribe = store.subscribe(()=>
			this.forceUpdate()
		);
	}

	componentWillUnMount() {
		this.unsubscribe();
	}


	render() {
		const props = this.props;
		const state = store.getState();

		return (
		  <Link
			  active = {
			    props.filter === state.visibilityFilter
			  }
			  onClick={() =>
				  store.dispatch({
			      type: 'SET_VISIBILITY_FILTER',
			      filter: props.filter
  		    })
    	  }
	    > {props.children}
			</Link>
		);
	}
}

```

Thus, the **Link** Component only takes care of the *appearance* based on `active` props. It doesn't know about the *behaviour*
The **FilterLink**  is a **Container Component** which provides *data* and *behaviour* to the **Presentational Component (Link)** .

When it mounts, it subscribes to the store, so it independently re-renders whenever the store state's changes.
Instead of providing its own DOM Tree, it delegates the rendering to the **Presentational Component (Link)**.

It's only job is to calculate the props based on the **FilterLink**'s own props and the CurrentState of the Redux Store.

And it also specifies the callback that are going to dispatch the action to the Store.
After action is dispatched, the store will remember the new state returned by the Reducer, and it will call every Subscriber to the store.

In this case, every **FilterLink** Component instance is subscribed to the store, so they will all have their `forceUpdate()` called on them.
And they will re-render according to the Current Store State.

**FilterLink** is self sufficient that it can be used in any component, with out needing to pass any props to it.

This makes the entire **Footer** Component simple and decoupled from the behaviour.


## 23. Extracting Container Component (Visible TodoList, AddTodo)

[JS Bin Demo](http://jsbin.com/yasuca/edit?html,js,console,output)

Lets start with extracting Container Component from **TodoList** to make it as a Presentational Component.

```
<TodoList
  todos = {getVisibleTodos(todos, visibilityFilter)}
   onTodoClick = {id =>
      store.dispatch({
        type: 'TOGGLE_TODO',
        id
      })
  } />
```

I want to encapsulate currently visible todos, into a separate **ContainerComponent** which connects to the Redux Store.

```
class VisibleTodoList extends Component {

	componentDidMount() {
		this.unsubscribe = store.subscribe(()=>
			this.forceUpdate()
		);
	}

	componentWillUnMount() {
		this.unsubscribe();
	}

  render() {
    const props = this.props;
    const state = store.getState();

		return(
    <TodoList
      todos={
        getVisibleTodos(
          state.todos,
          state.visibilityFilter
          )
      }
      onTodoClick={id=>
        store.dispatch({
          type: 'TOGGLE_TODO',
          id
          })
      }
      />
    );
  }
}
```

We no longer need to pass the props from the top. Lets update the `TodoApp` component with this newly created `VisibleTodoList` component.

```
const TodoApp = ({todos, visibilityFilter}) => (
      <div>
  			<AddTodo
  			      onAddClick={text=>
  			        store.dispatch({
  			          type:'ADD_TODO',
  			          id:nextTodoId++,
  			          text
            		})
  			      }
  	    />
        <VisibleTodoList />
				<Footer	/>
      </div>
    );
```

In previous section, we made `AddTodo` as a **Presentational Component**. Now let's  put back the behaviour to the `AddToDo`

There isn't much behaviour and presentation in this component; in future we may need to extract the presentation based on the complexity.

```
const AddTodo = () => {
	let input;

	return (
      <div>
        <input ref = { node => {input = node;}}/>
        <button onClick = { () => {
                store.dispatch({
                  type:'ADD_TODO',
                  id:nextTodoId++,
                  text: input.value
                })
							input.value = '';
					}}>
						Add Todo
        </button>
			</div>
			);
};
```
None of the Container component needs props from State, so we can remove the props of the `TodoApp` Component.
This makes the Top Level component simpler.

```
const TodoApp = () => (
      <div>
  			<AddTodo />
        <VisibleTodoList />
				<Footer	/>
      </div>
    );
```

We can  remove  
- `render()` function which renders the `<TodoApp>` with the current state of the app.
- all the `props` that are related to the `state`
- `store.subscribe(render)`
- `render()`


Because the ContainerComponent are going to Subscribe to the stores themselves.

So the following code

```
const render = () => {
  ReactDOM.render(
		<TodoApp {...store.getState()}/>,
	  document.getElementById('root')
  );
};

store.subscribe(render);
render();

```

becomes like this

```
  ReactDOM.render(
		<TodoApp/>,
	  document.getElementById('root')
  );
```

Separating the Presentational & Container Components is a good practice in most cases.
Only do this when it truly reduces the complexity of your code base.

As a good practice

- First extract the **Presentational Components**
- If there's too much boilerplate code, passing too many props, then you can extract **ContainerComponent** around them.

## 24. Passing the Store Down Explicitly via Props

[JS Bin Demo](http://jsbin.com/copufu/edit?html,js,output)

Managing store in a single file is not possible for RealTime Apps.
 - Single Store Reference makes Testing harder for Container Components. ( You may want a Mock Store for Testing Purpose)
 - Server Side Rendering becomes complex, where it has to use a different Store instance based on data.


Let us move the store creation code next to `ReactOM.render()`

```
const { createStore } = Redux;

const store = createStore(todoApp);

ReactDOM.render(
	<TodoApp />,
	document.getElementById('root')
);
```

Now lets inject the createStore(todoApp) to the `<TodoApp/>` component as follows

```
const { createStore } = Redux;

ReactDOM.render(
	<TodoApp store={createStore(todoApp)} />,
	document.getElementById('root')
);
```

Unfortunately we have to pass the store to every component as `props`.

```
const TodoApp = ({store}) => (
      <div>
			  <AddTodo store={store} />
        <VisibleTodoList store={store} />
				<Footer	store={store} />
      </div>
    );
```


Its less effort than passing props to the component; still its inefficient.
We'll find a better solution to this later; Let us proceed like this to focus on the problem.

The problem is that the **Container Component** needs to have the store instance to get the state from the dispatch actions and subscribe to the changes.

So we should update the **Container Components** to get the Store from `props`.

Lets start with `VisibleTodoList` by updating the `componentDidMount()` and `render()`

```
componentDidMount() {
  const { store } = this.props;
  this.unsubscribe = store.subscribe(()=>
    this.forceUpdate()
  );
}
```

```
render() {
  const props = this.props;
  const { store } = props;
  const state = store.getState();

  ....
  ....
}
```

Now the `VisibleTodoList` looks like this.

```
class VisibleTodoList extends Component {

	componentDidMount() {
    const { store } = this.props;
		this.unsubscribe = store.subscribe(()=>
			this.forceUpdate()
		);
	}

	componentWillUnMount() {
		this.unsubscribe();
	}

  render() {
    const props = this.props;
    const { store } = props;
    const state = store.getState();

		return(
    <TodoList
      todos={
        getVisibleTodos(
          state.todos,
          state.visibilityFilter
          )
      }
      onTodoClick={id=>
        store.dispatch({
          type: 'TOGGLE_TODO',
          id
          })
      }
      />
    );
  }
}
```

Let's follow the same steps to update remaining **Container Components**

**AddTodo**

```
const AddTodo = ({ store }) => {
	let input;

	return (
      <div>
        <input ref = { node => {input = node;}}/>
        <button onClick = { () => {
                store.dispatch({
                  type:'ADD_TODO',
                  id:nextTodoId++,
                  text: input.value
                })
							input.value = '';
					}}>
						Add Todo
        </button>
			</div>
			);
};
```

**Footer**
```
const Footer = ({ store }) => (
				<p>
           Show :
             {' '}
             <FilterLink filter = 'SHOW_ALL' store={store}>All</FilterLink>
             {' ,  '}
             <FilterLink filter = 'SHOW_ACTIVE' store={store}>Active</FilterLink>
             {' ,  '}
             <FilterLink filter = 'SHOW_COMPLETED' store={store}>Completed</FilterLink>
        </p>
);
```
**FilterLink**
```
class FilterLink extends Component {

	componentDidMount() {
    const { store } = this.props;
		this.unsubscribe = store.subscribe(()=>
			this.forceUpdate()
		);
	}

	componentWillUnMount() {
		this.unsubscribe();
	}


	render() {
		const props = this.props;
    const { store } = props;		
		const state = store.getState();

		return (
		  <Link
			  active = {
			    props.filter === state.visibilityFilter
			  }
			  onClick={() =>
				  store.dispatch({
			      type: 'SET_VISIBILITY_FILTER',
			      filter: props.filter
  		    })
    	  }
	    > {props.children}
			</Link>
		);
	}
}
```

Now all components read the Store from the props, and don't rely on the top level store.

This change did not affect the behaviour or the DataFlow of the application.

The **ContainerComponent** subscribes to the Store, just like before and updates the state in response to the changes.

What changed is how they access the store. Previously it accesses through top level variable, which doesn't scale much on Real World Applications.

That's why we pass down the store as `props`, so that the **ContainerComponents** can subscribe to it.

In future example, we'll see how to pass the store down to the **ContainerComponents** implicitly, but without introducing the top level variable.


## 25. Passing the Store Down Implicitly via Context
[JS Bin Demo] (http://jsbin.com/yuhusa/edit?html,js,output)

In the previous step, we got rid of the top level Store variable and passed it as a `prop` to the component.
We had to write lot of boilerplate code to pass down the store as `props`;

There is another way using the Advanced React feature called **Context**.

Let us create a new Component called `Provider`.

From its `render()` it just returns whatever it's the child is.
So we can wrap any Component under `Provider` & its gonna render the Component.

```
class Provider extends Component {
  render() {
    return this.props.children;
  }
}
```
I'm going to update the `render()` to
 - Render the `<TodoApp store={createStore(todoApp)}/>`  inside the `Provider`.
 - Move the `store={createStore(todoApp)} ` to the `<Provider store={createStore(todoApp)}>`

The `render()` will look like this.

```
ReactDOM.render(
  <Provider store={createStore(todoApp)} >
	 <TodoApp  />
  </Provider>,
	document.getElementById('root')
);
```

The `Provider` Component will use React's advanced Context feature to make the Store available to any Component inside it including grandChildren.

To do this, it has to define a special method called `getChildContext()`


```
class Provider extends Component {
  getChildContext() {
    return {
      store: this.props.store
    };
  }
  render() {
    return this.props.children;
  }
}
```

 `store:this.props.store` is the `store` passed through `<Provider store={createStore(todoApp)}>` which will be part of the Context that the `Provider` specifies for its any Children & Grand Children.

However, there is an important condition for the Context to work. i.e.,

- You've to specify `childContextTypes` on the Component(`Provider`) that defines `getChildContext()`

```
Provider.childContextTypes = {
  store: React.PropTypes.object
}
```

These are just `React.PropTypes` definitions. But unlike `PropTypes`, these `childContextTypes` are essential for the `Context` to be turned on.
If you don't specify them, no child components will receive this context.

The **ContainerComponents** currently access the store through the props. Now you can access through `Context`

```
class VisibleTodoList extends Component {

	componentDidMount() {
    const { store } = this.context;
		this.unsubscribe = store.subscribe(()=>
			this.forceUpdate()
		);
	}

	componentWillUnMount() {
		this.unsubscribe();
	}

  render() {
    const props = this.props;
    const { store } = this.context;
    const state = store.getState();

		return(
    <TodoList
      todos={
        getVisibleTodos(
          state.todos,
          state.visibilityFilter
          )
      }
      onTodoClick={id=>
        store.dispatch({
          type: 'TOGGLE_TODO',
          id
          })
      }
      />
    );
  }
}
```

We've to specify `contextTypes` for the ContainerComponent.

```
VisibleTodoList.contextTypes = {
  store: React.PropTypes.object
};
```

This is similar to `childContextTypes` but in this case we are specifying which `Context` to receive, not to pass down.
If we don't define the `contextTypes` then the component will not receive the relevant `Context`


What about the **FunctionalComponent** Which don't have `this`. e.g. `AddTodo`
It turns out that it also receives the `Context`, but as the second argument, after the `props`
```
const AddTodo = (props, { store }) => {
	let input;

	return (
      <div>
        <input ref = { node => {input = node;}}/>
        <button onClick = { () => {
                store.dispatch({
                  type:'ADD_TODO',
                  id:nextTodoId++,
                  text: input.value
                })
							input.value = '';
					}}>
						Add Todo
        </button>
			</div>
			);
};
```
Just like the ContainerComponent we need to specify `contextTypes` for this **FunctionalComponent** stating which `Context` it wants to receive.
In this case, its the `store`.

```
AddTodo.contextTypes = {
  store: React.PropTypes.object
};
```


Let us update `FilterLink` Component now in the same way, to set the Context.

```
class FilterLink extends Component {

	componentDidMount() {
    const { store } = this.context;
		this.unsubscribe = store.subscribe(()=>
			this.forceUpdate()
		);
	}

	componentWillUnMount() {
		this.unsubscribe();
	}


	render() {
		const props = this.props;
    const { store } = this.context;		
		const state = store.getState();

		return (
		  <Link
			  active = {
			    props.filter === state.visibilityFilter
			  }
			  onClick={() =>
				  store.dispatch({
			      type: 'SET_VISIBILITY_FILTER',
			      filter: props.filter
  		    })
    	  }
	    > {props.children}
			</Link>
		);
	}
}
```

```

FilterLink.contextTypes = {
  store: React.PropTypes.object
};

```

As the `FilterLink` is self contained to get the `store` from the `context`, we don't have to pass them through the `Footer`.
Let's get rid of it.

```
const Footer = () => (
				<p>
           Show :
             {' '}
             <FilterLink filter = 'SHOW_ALL'>All</FilterLink>
             {' ,  '}
             <FilterLink filter = 'SHOW_ACTIVE'>Active</FilterLink>
             {' ,  '}
             <FilterLink filter = 'SHOW_COMPLETED'>Completed</FilterLink>
        </p>
);
```

Also the `TodoApp`

```
const TodoApp = () => (
      <div>
			  <AddTodo />
        <VisibleTodoList />
				<Footer />
      </div>
    );

```

Context is a powerful feature, but in a way it contradicts the `React` Philosophy of the explicit data flow.
The Context is essentially a Global Variable across the Component tree, but Global Variables are usually bad idea.

Unless you use it for Dependency Injection like here what we did to pass a single variable to different components, you shouldn't use it.

Finally, the context API is not stable in React. It has changed before & likely to change again.
So try your best not to rely on context too much.


## 26. Passing the Store Down with &lt;Provide&gt; from React Redux

[JS Bin Demo](http://jsbin.com/tulece/edit?html,js,output)

In previous step, we've implemented `Provider` which uses React's Context to make the `store` available for every component it wraps.

It's so convenient that you don't need to write this `Provider` implementation yourself.

You can use a special library  `react-redux`

```
  <script src="https://cdnjs.cloudflare.com/ajax/libs/react-redux/4.0.0/react-redux.js"></script>
```

This is redux binding to the react library.


Get rid of the `Provider` implementation we wrote with the following piece of code.

```
const { Provider } = ReactRedux;

// import { Provider } from 'react-redux'; // For ES6/Babel, npm
// var Provider = require('react-redux').Provider; // For ES5 Code

```


## 27. Generating Containers with `connect()` from React Redux

[JS Bin Demo](http://jsbin.com/necaka/edit?html,js,output)

We are going to write the `VisibleTodoList` component in a different way.


```
const mapStateToProps = (sate) => {
  return {

  };
}
```

`mapStateToProps` takes the Redux's Store state and returns the  `props` it needs to pass to the Presentational `TodoList` component, to render it with the current state.

In this case, its a singel `props` called `todos`. Let us copy paste it from the `TodoList` component.

It returns the `props` the dependent on the current `state` of the Redux Store.

```
const mapStateToProps = (state) => {
  return {
    todos: getVisibleTodos(
              state.todos,
              state.visibilityFilter
              )
  };
};
```

Let us create another function `mapDispatchToProps`, and it accepts `dispatch` method from the `store` as only argument.
It returns the `props` thats should be passed to the `TodoList` component and that dependent on the `dispatch` method.

The only `prop` which is dependent on `dispatch` method is `onTodoClick`. Let's copy that from `TodoList` Component.

Note that we will not have reference to `store` anymore.
So instead of using `store.dispatch()` we are going to use `dispatch` which is provided as an argument to the `mapDispatchToProps`

```
const mapDispatchToProps = (dispatch) => {
  return {
    onTodoClick: (id) => {
      dispatch({
        type: 'TOGGLE_TODO',
        id
      })
    }
  };
};
```

Now we have 2 different functions.

- `mapStateToProps` - maps the Redux Store state to the `props` of the `TodoList` Component, that are related to the data from the redux store.

- `mapDispatchToProps` - maps the `dispatch()` of the store, to the callback props of the `TodoList` Component, it specifies the behaviour of which callback props dispatches which action.

These 2 functions together can define a **ContainerComponent**.
So instead of writing our Own **ContainerComponent**, we can generate the same using `react-redux`'s `connect`.

```
const { connect } = ReactRedux;
// import { connect } from 'react-redux'; // ES6, Babel
```

Let us create a variable instead of class, and we'll use the `connect()` method to obtain value for the variable.


```
const VisibleTodoList = connect(
	mapStateToProps,
	mapDispatchToProps)(TodoList);
```

Notice that this is a `connect()` I've to call it once again and this time,
I pass the **PresenationalComponent** that I want to wrap and pass the props to.

This `connect()` will create the same component, which we created earlier from scratch.

Hence I don't have to write the code to subscribe to the store or to specify the Context Type, because the `connect` function takes care of it.


## 28. Generating Containers with connect() from React Redux (AddTodo)

[JS Bin Demo](http://jsbin.com/fukira/edit?html,js,output)
