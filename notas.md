
**Indice**

- [React](#react)
  - [Documentación](#documentaci%C3%B3n)
    - [Props](#props)
    - [States](#states)
      - [Changing _props_ and _state_](#changing-_props_-and-_state_)
    - [Flux](#flux)
    - [Redux](#redux)
      - [Three Principles](#three-principles)
  - [Links a videos](#links-a-videos)
  - [Código de ejemplo](#c%C3%B3digo-de-ejemplo)
  - [Comparativas](#comparativas)
    - [vs. Angular2](#vs-angular2)
    - [React.Component vs React.createClass vs. Functional stateless components](#reactcomponent-vs-reactcreateclass-vs-functional-stateless-components)
  - [Tools](#tools)
    - [Building](#building)
  - [Testing](#testing)
- [Angular 2](#angular-2)
  - [Documentación](#documentaci%C3%B3n-1)
  - [Cheatsheet](#cheatsheet)
- [ES6 (ES2015)](#es6-es2015)
  - [link a videos](#link-a-videos)
- [Ionic 2](#ionic-2)
  - [Documentación](#documentaci%C3%B3n-2)
  - [Código de ejemplo](#c%C3%B3digo-de-ejemplo-1)


# React
## Documentación
la página oficial de React tiene un [tutorial](https://facebook.github.io/react/docs/tutorial.html) y un [thinking in react](https://facebook.github.io/react/docs/thinking-in-react.html)

### Props
props (short for properties) are a Component's configuration, its options if you may. They are received from above and immutable as far as the Component receiving them is concerned.

A Component cannot change its props, but it is responsible for putting together the props of its child Components.

### States

The state starts with a default value when a Component mounts and then suffers from mutations in time (mostly generated from user events). It's a serializable* representation of one point in time—a snapshot.

A Component manages its own state internally, but—besides setting an initial state—has no business fiddling with the state of its children. You could say the state is private.

#### Changing _props_ and _state_

- | _props_ | _state_ | 
--- | --- | --- 
Can get initial value from parent Component? | Yes | Yes
Can be changed by parent Component? | Yes | No
Can set default values inside Component?* | Yes | Yes
Can change inside Component? | No | Yes
Can set initial value for child Components? | Yes | Yes
Can change in child Components? | Yes | No

### Flux
The concept "Flux" is simply that your view triggers an event (say, after user types a name in a text field), that event updates a model, then the model triggers an event, and the view responds to that model's event by re-rendering with the latest data. That's it.

### Redux
Redux is like Flux in several ways, but it's different as well. It has these pieces:
- Provider: wraps your application, injecting the store
- Store: one large store that contains the state for your entire application
- Reducers: reducers listen to actions and make changes on the store values. They also cannot mutate the data on the store in any way, but must return a new set of data.
- Actions: pretty much just like flux actions, the only difference is that async can be handled in multiple different ways depending on store "middleware"
- Components: React components can be injected with various pieces of store data. React components also trigger Redux actions. This is what makes it all come together.
- Redux does not have the concept of a Dispatcher
- Redux assumes you never mutate your data

#### Three Principles
* Single source of truth (store)

  The state of your whole application is stored in an object tree within a single store.

* State is read-only (action)

  The only way to mutate the state is to emit an action, an object describing what happened.

  Actions are just plain objects

* Changes are made with pure functions (reducers)

  To specify how the state tree is transformed by actions, you write pure reducers.

  Reducers are just pure functions that take the previous state and an action, and return the next state.
  ** Remember to return new state objects, instead of mutating the previous state **

**Actions**

Hay Actions que son simple objectos y Actions Creators, que son funciones que crean Actions, esto está bueno para testearlas ej
```javascript
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

**Reducers**
> Actions describe the fact that something happened, but don’t specify how the application’s state changes in response. This is the job of a reducer.  

Son una función que toman el estado previo la acción y devuelven el nuevo estado
```
(previousState, action) => newState
```

cosas q no hay q hacer en un Reducer
* Mutate its arguments;
* Perform side effects like API calls and routing transitions;
* Call non-pure functions, e.g. Date.now() or Math.random().

La primera vez q nos llame Redux el State va a ser undefined entonces hay q contemplar eso y retornar un estado inicial, se puede usar es6 y usar el default value
```javascript
function todoApp(state = initialState, action) {
  // do something
  return state
}
```
A esto hay q agregarle el switch del type, y hacer el return de un nuevo objecto estado, q contenga lo mismo q antes más el cambio

ejemplo
```javascript
return Object.assign({}, state, {visibilityFilter: action.filter})
```
Lo más común es verlo con la sintaxis de los tres puntos
```
{ ...state, ...newState }
```
Para no usar el Object.assign({})

un ejemplo más completo cambiando este estado
```javascript
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
```
lo más dificil (me parece) es usar la sintaxis nueva, y modificar lo que realmente necesito, creo importante para esto tener unos buenos test case

```javascript
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })    
    default:
      return state
  }
}
```
Si tengo muchos reducers, para acomodarlo, puedo tenerlos en diferentes archivos y luego uno los agrupa a todos con combineReducers
```
import { combineReducers } from 'redux'

const todoApp = combineReducers({
  visibilityFilter,
  todos
})

export default todoApp
```

y con eso ya puedo crear el **store**
```javascript
let store = createStore(todoApp)
```

el ciclo de vida:

1) ante un evento tira una acción store.dispatch(action)

2) El store llama al reducer que le pasé

3) Cuando termina el Store llama al subscribe (cada uno que se registro se entera)

4) Cualquiera que esté suscripto puede llamar al store.getState y obtiene el nuevo estado


Se habla de **Presentational Components** y **Container Components**, los primeros son componentes comunes de React (o ReactNative) sin ninguna contaminación de Redux, los 2dos, sí son los q conectan los primeros componentes con Redux.

Technically, a **container component** is just a React component that uses **store.subscribe()** to read a part of the Redux state tree and supply props to a presentational component it renders

**connect()**
Si bien puedo escribir un container component a mano, se recomienda usar la función connect() de React Redux, para esto hay que definir **mapStateToProps** que recibe el state y devuelve un objeto que tiene las propiedades, actualizadas con el estado, del componente q voy a conectar (sería la parte del subscribe), además necesito la otra pata que es la que hace el dispatch del Action, para esto tenemos **mapDispatchToProps** que recibe el dispatch y retorna un objeto que tiene las propiedades callback del componente que estamos wrappeando. 

Ej:
```javascript
import { connect } from 'react-redux'

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)

export default VisibleTodoList
```
Para que esto funcione necesitan que tenga el store inyectado en todos los container components, esto se hace a través de un componente de React Redux llamado Provider

ej:
```javascript
let store = createStore(todoApp)

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```


## Links a videos
LearnCode.academy es un canal de youtube q tiene varios tutoriales copados. De React tiene 8 Videos, luego otros 6 de Flux y 6 más de Redux

arranca acá [Link](https://www.youtube.com/watch?v=MhkGQAoc7bc&list=PLoYCgNOIyGABj2GQSlDRjgvXtqfDxKm5b)

De Webpack también tienen uno muy bueno [Link](https://www.youtube.com/watch?v=9kJVYpOqcVU)

## Código de ejemplo

https://github.com/learncodeacademy/react-js-tutorials

## Comparativas

### vs. Angular2
Hay muchos tutoriales en internet que hablan de esto, dentro de los puntos más destacados (más allá de que uno es un framework y el otro una librería)
* Ambos están orientados a componentes
* Angular sigue usando two-way data binding
* Angular tiene JS en los HTMLs (incluso la sintaxis es más compleja q en el 1, con sus corchetes, asteriscos, etc) y React tiene HTML (en realidad JSX) en los JS

### React.Component vs React.createClass vs. Functional stateless components
como se aconseja más hacer **extends React.Component** que **React.createClass**, me apoyo en este [Link](https://toddmotto.com/react-create-class-versus-component/) q me muestra las diferencias entre ambos.
También están los **functional stateless components** que es una forma de escribir componentes q no necesitan extender de Component ya que no tienen que sobreescribir ningún método, un ejemplo:
```javascript
const Todo = ({ onClick, completed, text }) => (
  <li
    onClick={onClick}
    style={{
      textDecoration: completed ? 'line-through' : 'none'
    }}
  >
    {text}
  </li>
)
```

## Tools
### Building
Se usa bastante Webpack
lo instalo con 
```bash
npm install --save webpack
```
tambien tengo un global para ejecutar comandos
```bash
npm install -g webpack
```
y hay un webpack-dev-server entonces no tengo q estar armando un nodejs con express o algo de eso

webpack-dev-server --content-base src

## Testing

avajs https://github.com/avajs/ava

jest  https://facebook.github.io/jest/docs/getting-started.html

mocha http://www.hammerlab.org/2015/02/14/testing-react-web-apps-with-mocha/


# Angular 2
## Documentación
en el sitio hay un tutorial de quickstart
https://angular.io/docs/ts/latest/quickstart.html
q se puede bajar de https://github.com/angular/quickstart/

```
git clone https://github.com/angular/quickstart.git firstAngular2
```

lo levanto y todo ok

ahora voy a seguir este tuto
https://angular.io/docs/ts/latest/tutorial/


## Cheatsheet
https://angular.io/docs/ts/latest/guide/cheatsheet.html

# ES6 (ES2015)
Agrego 

It's important to know that this in an arrow function references the parent, it doesn't define a new this context.
```javascript
function updateTime() {  
    var _this = this;
    var time = new Date();
    setInterval(function () { return _this.time = new Date(); }, 1000);
}
```
With the arrow function syntax, this becomes:
```
function updateTime() {  
    var time = new Date();  
    setInterval(() => this.time = new Date(), 1000);
}
```
## link a videos
https://www.youtube.com/playlist?list=PLoYCgNOIyGACDQLaThEEKBAlgs4OIUGif

# Ionic 2

## Documentación

Un buen tutorial para arrancar: http://gonehybrid.com/build-your-first-mobile-app-with-ionic-2-angular-2/

## Código de ejemplo
Ionic 2 Conference Application
https://github.com/driftyco/ionic-conference-app

