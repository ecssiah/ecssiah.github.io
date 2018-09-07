---
layout: post
title:      "Keeping Time"
date:       2018-09-07 18:25:37 +0000
permalink:  keeping_time
---


Utilizing redux's action/reducer system and some built-in javascript functionality, it is possible to create actions that continue to update in the background of a react app, but there are some details that need to be considered to avoid errors.

Javascript has a collection of functions that are attached to the main window object that allow a custom function to be executed at regular intervals. This is nice, because in many other programming environments this framework usually has to be built or included manually. Anyone who has ever tried to write a game immediately comes up against this problem with producing a consistent callback feature for updating the game.

The javascript window object contains two matching matching timing functions: `setInterval` and `clearInterval`. `setInterval` is used to begin a regular callback, and clear interval is used to end this cycle. There is another related function called `setTimeout` that allows a function to be called *once* after a specified interval of time has elapsed.

I was working on a react app that required a countdown timer that would allow me to dispatch actions and update the redux state on every "tick" of the timer. The first thing I did was set up a button that allowed me to start the timer. In the `onClick` handler for that button is where I made the call to `setInterval`.

The `setInterval` function take two arguments. The first is the callback function you want executed every cycle, and the second is the length of the interval you want between each call in milliseconds. Therefore, if you want a function called `this.handleTimerUpdate` to be called every second, you would make this call:

`setInterval(this.handleTimerUpdate, 1000)`

There is no need to import anything for this to work, because `setInterval` is a function built into the main javascript library.

The `setInterval` function returns a reference to the timer that needs to be held in order to eventually stop the cycle using `clearInterval`. In my case, I assigned this reference to a `timer` variable in the local state of this React component:

```
this.setState({
  timer: setInterval(this.handleTimerUpdate, 1000),
	...this.state,
})
```

Now I'm setup to be able to retrieve this variable from the local state and end the cycle of callbacks whenever I need to. In my case, I needed the timer to stop in a few different situations. The first was when the countdown hit zero. This can be done directly from inside the callback function that is being called.

A rough version of what I did was this:

```
handleTimerUpdate() {
  if (this.state.timer < SECOND) {
	  clearInterval(this.state.timer)
	} else {
	  // do other updating based on tick
	}
}
```

`SECOND` is just a constant to relates our normal seconds to unit time where a minute is equal to 1 (i.e. `SECOND = 1/60`). This stops the callback function from continuing to be executed, and therefore stops the timer.

The last thing I'll mention is that you can run into a little bit of trouble if the user switches to another route or does anything else that causes the component with the timer to unmount. The trouble will occur if your callback function is trying to update the local state of the component, because an unmounted component no longer has access to its state.

The solution is to grab one of the React lifecycle methods, and put another call to `clearInterval` into that method. The `componentWillUnmount` method is called before the component is about to be unmounted, so it is perfect for this.

In my case, this also offered an excellent chance to quickly persist any changes to the local state into the database, so that the user doesn't lose any updates if they accidentally unmount the component by going to another route.

```
componentWillUnmount() {
  clearInterval(this.state.timer)
	this.props.saveFocus(this.props.focus)
}
```

The `saveFocus` function is created with a `mapDispatchToProps` function and the `connect` function from `'react-redux'`. It simply dispatches an action that asyncronously persists the information in `this.props.focus` into the database.

