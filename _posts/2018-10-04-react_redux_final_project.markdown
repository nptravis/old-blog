---
layout: post
title:      "React Redux Final Project"
date:       2018-10-04 04:06:41 +0000
permalink:  react_redux_final_project
---


This is my final blog post as student of Flatiron School! It's been an incredible journey, learned a ton, and can't wait for the next step. This will be a presentation of my final portfolio project, a React-Redux app built on top of a rails backend.

## Summary


I created a report card comment engine for teachers. Here at the school I currently work at teachers need to write 300+ report card comments for students every semester. It is an immense workload and I thought, maybe a web app could help them out! I devised a pretty simple idea to navigate a database of pre-written comments and compile them together in a master comment that can then be copy-pasted into the educational software PowerShool. I wanted to make it sensical, user friendly and quick. So, a SPA design seemed best.


## Things I Learned

This was built with react, and redux with a Rails api backend. I learned a TON via this project: React lifecycles and state, Redux middleWare, React routes, how to create a rails app of an API backend, wicked new ES6 syntax; it was awesome. Here are some highlights:

### React Routing

I used react-router-dom:
```
npm i --save react-router-dom
```

Took a bit to get my mind around it, but in react-router, a Route is just way to render components based on the url-location. So, the page never refreshes (reminds me of turbolinks), it just renders different components depending on which link was clicked. It doesn't actually "route" anywhere, pretty slick. 

So, I used my App.js as a master router, check it out:

```jsx
import {BrowserRouter as Router, Route } from 'react-router-dom'

class App extends Component {

  render() {
    const isAuthenticated = this.props.session.auth.isAuthenticated;
    const currentUser = this.props.session.current_user;

    const userRoutes = (
      <div className="App-main-container">
        <Route path="/" component={ showHeader } />
        <Route exact={true} path="/" component={ showUserHome } />
        <Route exact={true} path='/comment-builder' component={ showBuilder } />
        <Route exact={true} path="/logout" component={ showLogout } />
        <Route exact={true} path="/comments" component={ showComments } />
        <Route path="/" component={ showFooter } />
      </div>
    )

    const guestRoutes = (
      <div className="App-main-container">
        <Route path="/" component={ showHeader } />
        <Route exact={true} path="/" component={ showGuestHome } />
        <Route exact={true} path="/signup" component={ SignupForm } />
        <Route exact={true} path="/login" component={ LoginForm } />
        <Route exact={true} path="/logout" component={ showLogout } />
        <Route path="/" component={ showFooter } />
      </div>
    )

    return (
      <Router>
      <div className="App">
      
        { isAuthenticated ? userRoutes : guestRoutes }
       
      </div>
      </Router>

    );
  }
}

```

As you can see, I have guest routes, and user routes. Only the user Routes ( which are just component render-ers) get are available when isAuthenticated is true, via this code:

```
 { isAuthenticated ? userRoutes : guestRoutes }
```

Now, if a Route's has the attribute exact={true}, it will only render on it's exact path, but if not will render as long as part of the path matches it's path. So, used that feature to create a layout:
```
<Route path="/" component={ showHeader } />
<Route path="/" component={ showFooter } />
```
The header and footer will always get rendered, because the root path is in every url.

I built a bunch of functional components in this file that correspond to each route, like so:
```
<Route exact={true} path="/" component={ showUserHome } />

const showUserHome = () => (
  <main>
    <h1>Welcome, let's rock some comments!</h1>
  </main>
  )
```

So, now I can have any route render any component just by changing the Route path and component. 

### React Component Lifecycles

We learned about component lifecycles in the curriculum, and it really seems to be the heart of learning react. I can already tell mastering these component lifecycles if really the key here. Here is where I found some of them useful:

#### Using lifecycle methods for Fetch requests

I did a couple fetches from componentDidMount:

```
componentDidMount(){
		this.props.dispatch(fetchComments());
	}
```
sends an action to dispatch that does the fetch async, using Thunk, like so:

```
export function fetchComments() {

  return function (dispatch) {
  	
    dispatch({ type: 'LOADING_COMMENTS' });

    return fetch("http://localhost:3001/comment-partials")
    .then(response => response.json())
    .then(comments => 
        
      	dispatch({ type: 'FETCH_COMMENTS', payload: comments} )

      	);
  };
}

```

This is super useful, as the component is already rendered, the user gets to see stuff, and the fetch is happening quietly in the background. Great for a smooth UX. 

But, sometimes I wanted the fetch to get started earlier than that, so i used componentWillMount:
```
componentWillMount(){
		fetch(`http://localhost:3001/users/${this.props.userId}/comments`)
		.then(response => response.json())
		.then(response => {
			console.log("THIS IS THE RESPONSE:", response)
			this.setState({
				comments: response
			})
		})
	}
```
I wanted this data before the component actually mounted, so I made a synchonous fetch request before it renders.




- what this section is about
- why it matters
- research or examples
- takeaways

## New Awesome ES6 syntax

Javascript is really getting awesome. I love the new syntax. Here are my favorites:

### Arrow functions

These are indespensible in React. You can call a function without loosing your this binding, like so:
```
handleDelete = event => {
		event.stopPropagation()
		this.props.deleteTab(event)
	}
```
Since we aren't naming a new function, and just immediately invoking it, it doesn't create a new scope, therefore your 'this' stays the same. 

### Spread Operator

Again, indispensible in React. I used them a ton in redux when updating state, as we all know, can't be mutated. Check it out:

```
case "FETCH_COMMENTS":
			const comments = action.payload.map(comment => comment);
			let categories = comments.map(comment => comment.categories)
			categories = [].concat(...categories)
			let anotherArray = [...new Set(categories)]
			return {...state, loading: false, comments: [...state.comments, ...comments], categories: [...state.categories, ...anotherArray]};
```
spread operator all over the place! Here is a cool one:

```
let anotherArray = [...new Set(categories)]
```
This, another ES6 addition, uses the Set object, which let's you store 'unique' values of any type. It will filter your arrays for only unique values, finally!

### const and let

Now, I just never use var. const and let are blocked scoped, which can save you from tons of bugs. This basically means, they can't bleed into parent scopes.
like so:
```
 if (true) {
        let hello = "say Hello";
        console.log(hello);//"say Hello"
    }
   console.log(hello) // hello is not defined
```
vs var:
```
if (true) {
        var hello = "say Hello";
        console.log(hello); // "say Hello"
    }
   console.log(hello) // "say Hello"

```

Let also hoists differently than var:

```
hello = "say hello";
console.log(hello)


let hello; // Error: hello is not defined
```
vs var:
```
hello = "say hello";
console.log(hello)


var hello; // "say Hello"
```
It also will not allow re-declaration of the same varible name, which can save alot of headaches if you forgot you already used that variable name before 

the difference between const and let is that const won't allow any re-assigment.

***

## Conclusion

This project was super fun, and really made me realize how far I have come. I can now wire up an entire stack with Rails, or with a React front end. Pretty cool. I can't wait to get out there and build build build! Thank you for reading!

