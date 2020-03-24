---
layout: post
title:      "Keeping It Under Control"
date:       2020-03-24 05:20:31 -0400
permalink:  keeping_it_under_control
---

## Controłled Forms in React & Redux
Forms can be a bit of a hassle. They can be very finnicky to set up, they have a lot of individual bits and bobs, and if you don't set them up quite right to begin with, it can sometimes be difficult to figure out why you're not getting good data. 

Thankfully, React has an excellent way for simplifying our handling of forms. Since we can maintain state in the component itself, we don't have to worry about the format of the data that the form is sending off, because we can control it every step of the way.

Let me show you what I mean.

## A Simple Example
Here is a very simple HTML snippet of a form that takes submits a user's name:
```
<form>
  <label for="name>Name: </label>
  <input type="text" name="name" />
  <input type="submit" value='Submit Name' />
</form>
```

Normally, when working with a form we'd have to supply it with a method and action in order to send it to the server, but we wont worry about that for now.

Here is what that form would look like in a React component:
```
import React, { Component } from 'react';

export default class ExampleForm extends Component {
  render(){
    return(
      <form>
			  <label htmlFor="name>Name: </label>
        <input type="text" name='name'/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}
```
Note, we must use the htmlFor attribute in our label. React has done this to be consistent with the DOM property API.

Now, this form doesn't do anything yet. The first thing we want to do in React when dealing with a form, is set up the state. We only have one field, so that should be pretty simple. Since we usually want the form to begin empty, the state should also begin empty to reflect that.
```
import React, { Component } from 'react';

export default class ExampleForm extends Component {
  state = {
    name: ""
  }
  
  render(){
    return(
      <form>
        <input type="text" name='name'/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}
```
We are using this form to submit a name, so we have a name field in our state. Makes sense.

But once we start typing in the form, our state is no longer going to reflect what's in the text field. How can we fix that? With the magic of callback functions, of course!

Whenever a form element is changed, and onChange event can be triggered. We can access that in the following way:

```
import React, { Component } from 'react';

export default class ExampleForm extends Component {
  state = {
    name: ''
  }

  render(){
    return(
      <form>
        <input type="text" name='name' onChange={}/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}
```
See how we've added onChange as an attribute in the text field? Whatever is put into the brackets there will be called every time text field changes, with an onChange event as a parameter. Pretty cool, huh? But that means we need to define a method to handle the event. It's fairly common to see these event handling methods called something like 'handleOnChange', so we will do the same.
```
import React, { Component } from 'react';

export default class ExampleForm extends Component {
  state = {
    name: ''
  }

  handleOnChange = event => {

  }

  render(){
    return(
      <form>
        <input type="text" name='name' onChange={this.handleOnChange}/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}
```
Notice that we call the method in the attribute with 'this.' before it, and that we don't use any parentheses, because we're not invoking the method there, were supplying it as a callback for when an event fires. Another way of writing the attribute would be
```
{event => this.handleOnChange(event)}
```
but that's just some extra keystrokes.

However, we still haven't actually done anything--what do we do with the event? Why, we setState of course!
```
import React, { Component } from 'react';

export default class ExampleForm extends Component {
  state = {
    name: ''
  }

  handleOnChange = event => {
    this.setState({
      name: event.target.value
    })
  }

  render(){
    return(
      <form>
        <input type="text" name='name' onChange={this.handleOnChange}/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}
```
We use this.setState because it is a big no no in React to try and set this.state directly.

The event we're using has the value being typed into the text field right in it, so we can use that to set the state. With every key stroke we're updating the state!

But we need to make sure we know what's in the form. This is where the 'control' comes in. The form should always reflect what is in the state of the component. This will be especially important if we ever want to have data that's dependent on other components or even other parts of the form. How can we make sure the form's data reflects the state?
```
import React, { Component } from 'react';

export default class ExampleForm extends Component {
  state = {
    name: ''
  }

  handleOnChange = event => {
    this.setState({
      name: event.target.value
    })
  }

  render(){
    return(
      <form>
        <input type="text" name='name' onChange={this.handleOnChange} value={this.state.name}/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}
```
This way, the value of the text field is ALWAYS set equal to the name value in the state. The form is now under our complete control!

![muahaha](https://media1.tenor.com/images/f0baca047a09af4fbd4419c52af05871/tenor.gif?itemid=4482837)

But our plans have not yet come to fruition--right now, when we hit submit, we get some bogus data tacked on to our URL, and thats about it.

We must also handle the submit event:
```
import React, { Component } from 'react';

export default class ExampleForm extends Component {
  state = {
    name: ''
  }

  handleOnChange = event => {
    this.setState({
      name: event.target.value
    })
  }

  handleOnSubmit = event => {
    
  }

  render(){
    return(
      <form onSubmit={this.handleOnSubmit}>
        <input type="text" name='name' onChange={this.handleOnChange} value={this.state.name}/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}
```
I went a little further with this one than last time, since you should be a bit more familiar by now. You can see that we've added and onSubmit attribute to the form tag itself, with a handleOnSubmit callback function. 

Where do we go from here? 

Well, first things first, we don't want the form mucking with our url any more:
```
import React, { Component } from 'react';

export default class ExampleForm extends Component {
  state = {
    name: ''
  }

  handleOnChange = event => {
    this.setState({
      name: event.target.value
    })
  }

  handleOnSubmit = event => {
    event.preventDefault()
  }

  render(){
    return(
      <form onSubmit={this.handleOnSubmit}>
        <input type="text" name='name' onChange={this.handleOnChange} value={this.state.name}/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}
```
Now all our plans are coming together. What we do next depends on what we want to do with this data--are we sending it to a backend api? Are we using it to create or update another component?

Whatever we're doing with the data, one thing we should keep in mind, is we don't have to worry about trying to parse any of it from what the html form is sending us, because we've kept control over it the entire time--all the data in the form, is in the state!

So when we ship off the data, we can use the state itself to send the data.

So if we were using the name to update a parent component with a callback prop, and we want the form to reset after we submit it, it would look something like this:
```
import React, { Component } from 'react';

export default class ExampleForm extends Component {
  state = {
    name: ''
  }

  handleOnChange = event => {
    this.setState({
      name: event.target.value
    })
  }

  handleOnSubmit = event => {
    event.preventDefault()
    this.props.setName(this.state.name)
    this.setState({
      name: ''
    })
  }

  render(){
    return(
      <form onSubmit={this.handleOnSubmit}>
        <input type="text" name='name' onChange={this.handleOnChange} value={this.state.name}/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}
```

If we're using Redux and sending the name to the store, it might look something like this:
```
import React, { Component } from 'react';
import connect from 'react-redux';
import { addName } from './actions/NameActions'

class ExampleForm extends Component {
  state = {
    name: ''
  }

  handleOnChange = event => {
    this.setState({
      name: event.target.value
    })
  }

  handleOnSubmit = event => {
    event.preventDefault()
    this.props.addName(this.state)
    this.setState({
      name: ''
    })
  }

  render(){
    return(
      <form onSubmit={this.handleOnSubmit}>
        <input type="text" name='name' onChange={this.handleOnChange} value={this.state.name}/>
        <input type="submit" value='Submit Name' />
      </form>
    )
  }
}

export default connect(null, { addName })(ExampleForm)
```

## Wrap Up
Hopefully that simple walkthrough was helpful in understanding just how we can control forms in React. It may not seem that useful with such a simple form, but when you have more complex forms with nested data, you'll be very thankful there's a (fairly) straightforward method for dealing with them in React. Happy coding!

