# React-State-Events

## SWBATs

* Learn the difference between props and state, and why one would use one or the other
* Instantiate state in and out of the constructor
* Trigger rerenders by calling setState
* Manipulate the DOM by changing values in state
* Create event handler callbacks that manipulate state


## Outline

     5m Introduction to State
    10m Using State
    10m Conditional Rendering
    10m Events in React
     5m Presentational vs Container Components
     5m CFU: Using state and events to update state
    ===
    45m Total

**Note: Painting data has been decreased in this lecture for easy scrolling**

### Introduction to State

Convert App component to class component from functional component

#### **Create State**

```js
//inside App.js
  constructor(){
    super()
    this.state = {
      color: "red"
    }
  }

  state = { color: "red"} //state outside constructor
```
Explain:
* What is a state?
     * State is a special attribute of an instance of a component and is typically accessed inside of a component by running this.state. Other attributes can be created for a component (e.g. this.beef = "steak"), but the name state is special
     * State is just an object containing key-value pairs
     * It is a reflection of the current state of a component (e.g. is this card currently flipped? should I render component X or component Y? what data am I currently carrying?)
     * Can be initialized in and out of the constructor
* How it is different from props?
     * props (short for “properties”) and state are both plain JavaScript objects. While both hold information that influences the output of render, they are different in one important way: props get passed to the component (similar to function parameters) whereas state is managed within the component (similar to variables declared within a function).
* Why one would use one or the other
     * Find details [here](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md)
* Difference between the above two syntaxes
* Show state in dev tools

#### **Display/Use State**

```js
//inside App.js

<NavBar
     color={this.state.color} //
     title="Paintr"
     icon="paint brush"
     description="an app we made"
/>
```
### Events in React

* React Synthetic Events
	* [List](https://reactjs.org/docs/events.html)
	* It is useful to enter a `debugger` and examine the `event` object and note for them that even though React generates special synthetic events, these are more or less the same as your typical event objects
	* Students often will try to put event handlers on their own components, so it is important that you tell them that event handlers can only be attached to built-in JSX components (e.g. div, p)
* Students should be able to draw on their knowledge of how to use events in JS
	* Come up with a feature (e.g. toggle, Konami code) and ask the class how they would have handled that in Vanilla JS 
	* They should mention that when the event handler is triggered, a DOM element must be found/created before updating/appending it to the DOM
	* Event triggering is the same, the difference is that rather than manually finding and editing DOM nodes, we will eventually call `setState` and let changes to state generate the desired DOM changes
* Event handler callbacks should be written as arrow functions to avoid losing context

#### **Update State**

```js
//inside App.js
changeColor = () => {
    this.setState({color: "yellow"}, () => console.log(this.state.color)) //setState is async

    console.log(this.state.color)
  }

 render(){
  return (
    <div>

      <NavBar
        color={this.state.color}
        title="Paintr"
        icon="paint brush"
        description="an app we made"
      />

      <button onClick={this.changeColor}>Change color</button>
      
      {/* <button onClick={() => this.changeColor()}>Change color</button> */}

      <PaintingsList paintings={paintings} />
       </div>
          )
      }
     }
```

Explain:
* How to update State?
     * State represents the paradigm shift of moving from imperative to declartive programming - whenever a problem requires some sort of DOM manipulation, the thought process should shift from obtaining/creating DOM elements to manipulating state and making your template (the JSX in render) depend on the values of state
     * `setState`
          * Changing the state object by ordinary assignment does nothing - mutating state directly will change the object's values, but the problem is that the `render` function of our component is not called, so the DOM will not respond to these changes
          * We use `setState` because in addition to changing the object, `setState` will call the `render` function, this time using the newly updated state values
          * Gotchas
               * Changing state is asynchronous. `console.log` the state value that was supposed to be set below `setState`
               * `setState` takes 2 arguments:
               * Either an object or a callback that accepts a parameter of the previous state and returns an object
               * A callback that can be called whenever `setState` is finished updating state and rerendering
               * `setState` does a shallow comparison, meaning that even without spreading or copying state, only the properties that are specified in the object received by setState are changed while the others remain intact. However, this is only true for that first layer of properties: nested objects will have their values overwritten

* Add `button` to NavBar component instead of App component and show how to pass `changeColor` function as a props and how state is updated when button is clicked

```js
//App.js

render(){
  return (
    <div>

      <NavBar
        color={this.state.color}
        title="Paintr"
        icon="paint brush"
        description="an app we made"
        changeColor={this.changeColor}
      />

      <PaintingsList paintings={paintings} />

    </div>
  )
  }
}

// NavBar.js
const NavBar = props => {
  return (
    <div className={`ui inverted ${props.color} menu`}>
      <a className="item">
        <h2 className="ui header">
          <i className={`${props.icon} icon`} />
          <div className="content">{props.title}</div>
          <div className="sub header">{props.description}</div>
        </h2>
      </a>
      <button onClick={props.changeColor}>Change color</button>

    </div>
  );
};

```

### Conditional Rendering

* If we write our JSX such that its values depend on state, we can use state as a proxy for the DOM, allowing _changes to state_ to progrmatically manipulate the DOM
* A toggle that depends on a boolean

**Deliverable:** Display/Hide a form to add new painting 

```js
//App.js
import PaintingForm from './PaintingForm'


class App extends React.Component{

  constructor(){
    super()
    this.state = {
      color: "red",
      formView: false
    }
  }


  changeColor = () => {
    this.setState({color: "yellow"}, () => console.log(this.state.color)) //setState is async

    console.log(this.state.color)
  }

  toggleForm = () => {
    this.setState({
      formView: !this.state.formView
    })
  }

  render(){
  return (
    <div>

      <NavBar
        color={this.state.color}
        title="Paintr"
        icon="paint brush"
        description="an app we made"
        changeColor={this.changeColor}
      />

      <button onClick={this.toggleForm}>Show/Hide new painting form</button>

      {this.state.formView ? <PaintingForm/> : <PaintingsList paintings={paintings} />}

    </div>
  )
  }
}

//PaintingForm.js

import React from 'react'

class PaintingForm extends React.Component{

    render(){
    return(
    <div>
        <h1> Add a new Painting</h1>
        <form>
            <input type="text" placeholder="ImgURL"/> <br/>
            <input type="text" placeholder="Title"/><br/>
            <input type="text" placeholder="Artist Name"/><br/>
            <input type="text" placeholder="date"/><br/>
            <input type="text" placeholder="width"/><br/>
            <input type="text" placeholder="height"/><br/>

            <input type="submit" value="add new painting"/>
        </form>

    </div>)
    }

}

export default PaintingForm

```

### Presentational vs Container Components

* There are 2 distinctions for components that are mostly overlapping, but slightly different
* Class vs Functional Components
	* This difference is focused more on syntax and is pretty self-explanatory: class components use class syntax and functional components are just functions that return JSX
* Container(Smart) vs Presentational(Dumb) Components
	* Containers contain most of the programming logic and/or are used to manage state. As they often need state and component lifecycle methods, containers are usually class components, though it is entirely possible to write a container component as a functional component, as in cases where the container needs a lot of logic, but makes no use of state
	* Presentational components contain little-to-no logic and are typically almost entirely dependent on their parent components for the data they use to display
	* Because of the way information trickles down from parent to child in a component hierarchy via props, fewer, more centralized sources of data and functionality are much more manageable at scale

Create state for paintings so in future lecture we can update the paintings state

```js
//APP.js
constructor(){
    super()
    this.state = {
      color: "red",
      paintings: paintings, //using state for paintings
      formView: false
    }
  }

render(){
  return (
    <div>

      <NavBar
        color={this.state.color}
        title="Paintr"
        icon="paint brush"
        description="an app we made"
        changeColor={this.changeColor}
      />

      <button onClick={this.toggleForm}>Show/Hide new painting form</button>

     {this.state.formView ? <PaintingForm/> : <PaintingsList paintings={this.state.paintings} />} {/*using state for paintings*/}

    </div>
  )
  }
```

### CFU: Using state and events to update state

Tasks:

1. Convert Painting component to class component
2. Create state for votes
3. Display votes for each painting and a button to add a vote
4. When `Add Vote` button is clicked increment vote and update the state
5. **Optional** Use `semantic ui` package to give some styling for votes functionality

Solution:
```js
//Painting.js

import React from 'react';

class Painting extends React.Component{

  constructor(props){
    super()
    this.state = {
      votes: props.painting.votes
    }
  }

  // state = {
  //   votes: this.props.painting.votes
  // }

  addVote = () => {
    this.setState({
      votes: this.state.votes + 1
    })
  }

  render(){
    return (
      <div>
        <img src={this.props.painting.image} />
        <h4>
          "{this.props.painting.title}" by {this.props.painting.artist.name}
        </h4>
        <p>Year: {this.props.painting.date}</p>
        <p>
          Dimensions: {this.props.painting.dimensions.width} in. x {this.props.painting.dimensions.height} in.
        </p>
        
        <span>Votes: {this.state.votes} </span>
        <button onClick={this.addVote}>Add Vote</button>

        {/* style
        <div class="ui labeled button" tabindex="0">
          <div class="ui red button" onClick={this.addVote}>
            <i class="heart icon"></i> Add Vote
          </div>
          <a class="ui basic red left pointing label">
            {this.state.votes}
          </a>
        </div> */}
      </div>
        
    );
  }
  
};

export default Painting;

```