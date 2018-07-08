---
layout: post
title:      "Location, Location, Location"
date:       2018-07-08 03:25:20 -0400
permalink:  location_location_location
---

I thought I would use this blog post to quickly cover a few different topics I came across while building a rails app with a JQuery frontend. They all broadly address the need to really understand the importance of where elements of an application are located in relation to each other.

**Turbolinks**

Turbolinks is a javascript library that is enabled by default when creating a new Rails application. This library speeds up the loading of views in the application by replacing some of the normal requests with AJAX requests. This allows a rails developer to get some of the benefits of a single-page application without needing to write a client-side Javascript framework. 

But the use of Turbolinks leads to some things that need to be handled differently when working with additional client-side scripts. The optimizations brought by Turbolinks are often able to completely bypass the need for a page refresh entirely when displaying a new view. The issue here is that the `document.ready` function will not be called after the view is loaded, because this function is attached to the page loading process.

To handle this problem, Turbolinks provides its own event that fires whenever its optimized loading process is complete. This function is named `turbolinks:load`. You can attach a callback function to this event in the same way an event is attached to the `document.ready` function:

```
$(document).on('turbolinks:load', function() {

  ...

});
```

As of Rails 5, this will also be called on the initial load of the page, so there is no need to also hook into the `document.ready` function. For previous versions, it is sometimes necessary to attach your load function to both events, like this:

```
$(document.on('ready turbolinks:load', function() { ... };
```

**Page-specific javascript**

There are a number of different ways to get javascript functions to run only on specific pages, but they each involve understanding some nuances.

The way I chose for this application was to call a global function from a script tag at the end of the page. Since the script tag will only be run when that particular page is loaded, this allows you to have some javascript functions that only run for that specific page.

At the bottom of the page, the function is called like this:

```
<script charset="utf-8">
  build_story_index()
</script>
```

The only other thing that needs to be done is to make sure the function that is being called is in the global scope. This is done by attaching the function to the `window` object when defining it in a javascript file:

```
window.build_story_index = function() {
  var req;
  req = $.get('/stories.json');
  req.done(function(data) {
    build_story_elements(data);
  });
};
```

It is not necessary for the functions used inside of this global function to also be global. These functions just need to be available in the same file where the global function is being defined.

**Html5 History API**

The last thing I'll cover is the new History API that has been added with Html5. This solved an old problem that had arisen once javascript was being used to make significant changes to a web page. Often, the page would be changed to such a degree that it would make sense to update the URL that is being displayed, even though no actual page reload had occurred. [This excellent article](https://css-tricks.com/using-the-html5-history-api/) from css-tricks explains the history behind this problem, and mentions some of the workarounds people had developed to deal with it before the History API was introduced. The whole article is worth reading, but, in reality, to make use of the History API, you'll likely only need to know one main function.

To add a new URL without a page reload ensure that it works with the back button, the `pushState` method is used. This method has three parameters:

```
window.history.pushState(null, null, '/stories/1')
```

The `pushState` method is accessed by grabbing the history object that is attached to the global window object. The first parameter is any data that will be needed when the user presses the back or forward button. The second parameter is a 'title', but, as of now, every major browser just ignores this parameter. The third parameter is the actual URL that is to be pushed to the address bar.

One catch is that this URL must have the same origin as the current one. Otherwise, it is considered a cross-site request. This is forbidden, because it is a major security risk. It could be used to fool a user into thinking they are at a different site than the one they were expecting to visit. Imagine showing someone a page that looks like the login page for their email, but is actually a different site ready to grab their username and password.

In many ways, being aware of where a piece of code is being executed in the broader landscape of an application is one of the most important skills a developer can build. Once the basics of a language and its constructs are well-understood, keeping track of how they all work together in an application may be considered the first step in becoming a competent programmer. It is the step that can take someone from solving isolated problems to building solid applications.
