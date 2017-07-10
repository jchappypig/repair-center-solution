# Repair center

A workshop for [WOMEN WHO CODE Sydney](https://www.womenwhocode.com/sydney), which will be held on July 12th 2017.

[https://www.meetup.com/Women-Who-Code-Sydney/events/240653030/](https://www.meetup.com/Women-Who-Code-Sydney/events/240653030/).

## Requirements
* node `^5.10.0`
* yarn `^0.23.0` or npm `^3.0.0`
* Editor of your choise, preferrable VSCode

> Only do the following if you don't have node and yarn installed
>
> ### Install homebrew
>
> ```bash
> $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
> ```
>
> ### Install node
>
> ```bash
> $ brew install nvm
> $ nvm install v6.8.1
> ```
>
> ### Install yarn
>
> ```bash
> $ brew install yarn
> ```
>
> ### Download VS Code
> [https://code.visualstudio.com/download](https://code.visualstudio.com/download)
>

## Installation

```bash
$ git clone git@github.com:jchappypig/repair-center-solution.git
$ cd repair-center
$ yarn
```

## Running the Project

```bash
$ yarn start
```

## Tutorial

### About

In this workshop, we will be using React and Redux to create web app for a repair center. Customer can visit the web app and log a ticket about the repairing devise, including its category, model and issue description.

### Requirements

The owner of the repair center has requested to build the web app with some sort of intelligence — based on what customer answered previously, the next set of questions should change dynamically.

Example flow is like this:

![Repair Center example flow](../master/src/assets/tutorial/repair-center.png?raw=true)

### Part1: Devise category
1. Create Category components (Category.js) under src/components

```js
import React from 'react'
import { Link } from 'react-router-dom'

const Category = () => (
  <div>
    <Link to='/model' className='btn btn-secondary d-block'>IPhone</Link>
    <Link to='/model' className='btn btn-secondary d-block'>Macbook</Link>
    <Link to='/model' className='btn btn-secondary d-block'>IPad</Link>
  </div>
)

export default Category
```
2. Create route to go to category page

Go to src/routes/index.js and add the following route

```js
import Category from '../components/Category'
```
...
```js
<Route path='/category' component={Category} />
```

![Category page](../master/src/assets/tutorial/categoryPage.png?raw=true)

3. Time for action and reducer

Create a file called ticket.js under src/**actionsAndReducers**, which will be in charge of storing actions and reducers for ticke state

```js
// ------------------------------------
// Constants
// ------------------------------------
export const SET_CATEGORY = 'SET_CATEGORY'

// ------------------------------------
// Actions
// ------------------------------------
export function setCategory (category) {
  return {
    type: SET_CATEGORY,
    category
  }
}

export const actions = {
  setCategory,
}

// ------------------------------------
// Action Handlers
// ------------------------------------
const ACTION_HANDLERS = {
  [SET_CATEGORY]: (state, action) => ({ ...state, category: action.category }),
}

// ------------------------------------
// Reducer
// ------------------------------------
const initialState = {}
export default function ticketReducer (state = initialState, action) {
  const handler = ACTION_HANDLERS[action.type]

  return handler ? handler(state, action) : state
}

```

5. connect reducer with redux store

Go to src/store/createStore.js

```js
import ticketReducer from '../actionsAndReducers/ticket'
```

Add ticket reducer to store

```js
  const store = createReduxStore(
    combineReducers({
      location: routerReducer,
      ticket: ticketReducer
    }),
    initialState,
    composeEnhancers(
      applyMiddleware(...middleware),
      ...enhancers
    )
  )
```


4. Create our first container CategoryContainer.js under src/**containers**

```js
import { withRouter } from 'react-router'
import { connect } from 'react-redux'
import Category from '../components/Category'
import { setCategory } from '../actionsAndReducers/ticket'

const mapDispatchToProps = {
  setCategory
}

export default withRouter(connect(null, mapDispatchToProps)(Category))
```

5. Update component Category.js to dispatch setCategory on click

...
```js
  <div>
    <Link to='/model' className='btn btn-secondary d-block' onClick={() => setCategory('IPhone')}>IPhone</Link>
    <Link to='/model' className='btn btn-secondary d-block' onClick={() => setCategory('Macbook')}>Macbook</Link>
    <Link to='/model' className='btn btn-secondary d-block' onClick={() => setCategory('IPad')}>IPad</Link>
  </div>
```
...

6. Nothing happened yet until we connect the route with container CategoryContainer instead of Category

Go to src/routes/index.js. Replace Category component with Category container. Because we need to pass the redux store to component through the container.

...
```js
import CategoryContainer from '../containers/CategoryContainer'
```
...
```js
<Route path='/category' component={CategoryContainer} />
```

![Category page with dipatched action](../master/src/assets/tutorial/categoryPageWithAction.gif?raw=true)

7. On UI, we need to show the button as active when a category is selected

We want know what category has been selected, so we need to pass the redux state to the Category component. So on src/containers/CategoryContainers.js

...
```js
const mapDispatchToProps = {
  setCategory
}

const mapStateToProps = (state) => ({
  ticket: state.ticket,
})

export default withRouter(connect(mapStateToProps, mapDispatchToProps)(Category))
```

and on Category component, we need to compare current button with the category we have selected. Go to src/components/Category.js and update

...
```js
import PropTypes from 'prop-types'

const Category = ({ ticket, setCategory }) => (
  <form>
    {['IPhone', 'Macbook', 'IPad'].map((category, index) => (
      <Link to='/model'
        className={`btn d-block btn-secondary ${category === ticket.category ? 'active' : ''}`}
        key={index} onClick={() => setCategory(category)}>{category}
      </Link>
    ))}
  </form>
)

Category.propTypes = {
  ticket: PropTypes.object.isRequired,
  setCategory: PropTypes.func.isRequired
}
```
....

![Category page with enable state](../master/src/assets/tutorial/categoryPageWithEnable.png?raw=true)


### Challenges
It's your turn to create a page that describe what model of your devise. For the start, you will need to have a few buttons to be chosen from - 'IPhone6s', 'IPhone7', 'IPhone6s Plus', 'IPhone7 Plus'.

![Model page](../master/src/assets/tutorial/modelPage.png?raw=true)

Hint:
* Reference code in Category page
* To do list: component, container, action, reducer, route
* solution is right afterward. DO NOT LOOK. Try it yourself first.

.

.

.

.

.

.

.

.

.

.

.

### Part2: Devise model
1. Create Model components (Model.js) under src/components

```js
import React from 'react'
import PropTypes from 'prop-types'
import { Link } from 'react-router-dom'

const Model = ({ ticket, setModel }) => (
  <form>
    {['IPhone6s', 'IPhone7', 'IPhone6s Plus', 'IPhone7 Plus'].map((model, index) => (
      <Link to='/issue-description'
        className={`btn btn-secondary d-block  ${model === ticket.model ? 'active' : ''}`}
        key={index} onClick={() => setModel(model)}>{model}
      </Link>
    ))}
  </form>
)

Model.propTypes = {
  ticket: PropTypes.object,
  setModel: PropTypes.func
}
```

2. Similar to Catetegory. We create container for Model - ModelContainer.js under src/containers

```js
import { withRouter } from 'react-router'
import { connect } from 'react-redux'
import Model from '../components/Model'
import { setModel } from '../actionsAndReducers/ticket'

const mapStateToProps = (state) => ({
  ticket: state.ticket,
})

const mapDispatchToProps = {
  setModel
}

export default withRouter(connect(mapStateToProps, mapDispatchToProps)(Model))
```

3. Add missing action **setModel** and reducer in src/actionsAndReducers/ticket.js

...
```js
export const SET_MODEL = 'SET_MODEL'
```
...
```js
export function setModel (model) {
  return {
    type: SET_MODEL,
    model
  }
}
```
...
```js
export const actions = {
  setCategory,
  setModel,
  setDescription,
}
```
...
```js
const ACTION_HANDLERS = {
  [SET_CATEGORY]: (state, action) => ({ ...state, category: action.category }),
  [SET_MODEL]: (state, action) => ({ ...state, model: action.model }),
}
```

4. Add route for /model

...
```js
import ModelContainer from '../containers/ModelContainer'
```
...
```js
<Route path='/model' component={ModelContainer} />
```

5. Finally, we need to make the model page a bit smart. It should be able to change the set of models according to different categories. The catetory has been stored in ticket state. We can make use of it and create a mapping.

```js
const models = {
  IPhone: ['IPhone6s', 'IPhone7', 'IPhone6s Plus', 'IPhone7 Plus'],
  Macbook: ['Macbook Pro', 'Macbook Air'],
  IPad: ['IPad Pro', 'IPad', 'IPad mini'],
}

const modelsForCategory = (category) => {
  return models[category] || models.IPhone
}
```

```js
const Model = ({ ticket, setModel }) => (
  <form>
    {modelsForCategory(ticket.category).map((model, index) => (
      <Link to='/issue-description'
        className={`btn btn-secondary d-block  ${model === ticket.model ? 'active' : ''}`}
        key={index} onClick={() => setModel(model)}>{model}
      </Link>
    ))}
  </form>
)
```



## Reference

* This project is build on top of boilerplate
[https://github.com/davezuko/react-redux-starter-kit](https://github.com/davezuko/react-redux-starter-kit)
