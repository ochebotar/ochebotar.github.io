---
layout: post
title: Tips for Writing Cleaner Code
cover-img: /assets/img/code-tips.jpeg
thumbnail-img: /assets/img/code-tips.jpeg
# gh-repo: daattali/beautiful-jekyll
# gh-badge: [star, fork, follow]
tags: [javascript, jQuery, coding, tips&tricks]
comments: true
---
I decided to write an article that will be useful for beginners to understand their mistakes and to put together some practices.

Of course, the number of such examples is very, very large, so I will limit myself to just a few.

### 1. Constants

This problem concerns not only javascript, but programming in general. Consider an example:

```js
$elem.on('keydown', function(e) {
  if (e.keyCode == 27) {
    //...
  }
});
```

What is the magic number 27? People who code often will notice — this is the ESC key. But most developers, especially beginners, either do not remember these codes, or do not know at all, and when faced with codes, they are forced to climb once again into the search system and waste time.
Of course you can add a comment in the code but it would be much more efficient to enter a constant, for example: KEY_ESC = 27

### 2. Identifiers

Often we need to get the identifier of an element (comment, post, user, etc.) in order to perform some actions (for example, evaluate the comment using ajax) and often you can find this approach:

```js
var id = $(this).attr('id').substring(8);
```

As in the previous example, the developer has to guess — what is this number 8.

Another example (this line is copied from a real project):

```js
var last_id = $('#answer_pid' + id + ' li:first div').attr('id').substr(7);
```

The slightest change in the html layout will brake the code.

Okay, there is another:

```js
<div class="comment" id="comment_123"></div>

var id = $(this).attr('id').substring("comment_".length);
```

This is better (at least there are no wired numbers), but still this approach too much binds the js code to html.

We can use the 'data-' parameters:


```html
<div class="comment" data-id="123"></div>
```

It much more easier to get the identifier now:

```js
var id = $(this).attr('data-id');
```

or

```js
var id = $(this).data('id');
```

___________

### 3. $.post

We all know a jQuery method for working with ajax — $ .ajax. There are several shorthand functions such as $ .get, $ .load, $ .post, etc.

These functions have been added specifically to facilitate frequently performed actions (upload a script, json, execute a post request), but in the implementation all these methods refer to $ .ajax.

Personally, I never use shorthand functions, and that’s why.

In beginners or inexperienced code you can find several different stages:

a. Starting

```js
$.post(url, data, function(data) {
  data = $.parseJSON(data);             
  //...
});
```

b. Adding the “try-catch” block

```js
$.post(url, data, function(data) {
  try {
      data = $.parseJSON(data);
  } catch (e) {
      return;
  }
  //...
});
```

c. We learn from the documentation that in $ .post the last parameter can be passed to the dataType (which disappears in tons of code if success function doesn’t fit into the screen).

```js
$.post(url, data, function(data) {
  //...
}, 'json');
```

I rarely can see error handlers in beginners projects, this is mainly due to laziness and unwillingness to spend extra 5 minutes of time, or the developers are just sure that there will be no errors.

If the developer decided to add an error handler to $ .post, it often be something like:

```js
$.post(url, data, function(data) {
  //...
}, 'json').error(function() {
  //... 
});
```

Which is really unreadable. Writing an error handler every time is a tedious task, so you can set up a default error handler for all ajax requests, for example:

```js
$.ajaxSetup({
  error: function() {
    // Error modal
  } 
});
```

Let’s return to $ .post. As shown above, using $ .post makes the code looks awful (especially with the dataType in an incomprehensible place). Lets rewrite the last example in $ .ajax.

In my opinion, this approach is more readable and easier to maintain.

```js
$.ajax({
  type: "POST",
  url: url,
  data: data,
  dataType: "json",
  success: function(data) {
    //...
  },
  error: function() {
    //...
  }
});
```

### 4. Event Handlers for Multiple Elements

Sometimes is necessary to add event handlers to page elements (for example, the “delete message” button). And often you can come across this approach:

```js
$('.comment a.delete').click(function(){
  //...
});
```

Now the problem is to add the same handler to a new element (for example, to a dynamically loaded comment). I saw a lot of solutions, including redefining all the handlers once again:

```js
$('.comment a.delete').unbind('click').click(function() {
  //
});
```

There is a method in jQuery 1.7 called “[on](https://www.w3schools.com/jquery/event_on.asp)”, which binds the event handlers, filtering the elements by the selector.

```js
$('body').on('click', 'a.external', function(e) {  
  // *The function will be called when you click on any link with the external class
});
```

Important to remember, that this handler works for dynamically created objects too. This approach should be applied wisely. For example, the following code can lead to performance degradation and slowdown of the browser:

```js
$('body').on('mousemove', selector, function() {
  //...
});
```

### 5. Namespaced events

The fact that namespaced events were added to jQuery 1.2 — they are few people use it (I think most people just don’t know about them).

For example, we have this code:

```js
$('a').on('click', function() {
  // Handler 1
}); 

$('a').on('click', function() {
  // Handler 2
});
```

Now, for example, we need to remove the second handler from the links. But $(‘a’).off(‘click’) will remove both handlers.

Lets use namespaced events:

```js
$('a').on('click.namespace1', function() {
  //Handler 1
}); 
$('a').on('click.namespace2', function() {
  //Handler 2
});
```

Now it becomes possible to remove the second handler by calling

```js
$('a').off('click.namespace2');
```

More information about namespaced events can be found [here](http://api.jquery.com/on/#event-names){:target="_blank"}.

This is only a small part of the problems that I regularly encounter in someone else’s code. I hope that this post will help improve the quality of the code.

<style>.bmc-button img{height: 34px !important;width: 35px !important;margin-bottom: 1px !important;box-shadow: none !important;border: none !important;vertical-align: middle !important;}.bmc-button{padding: 7px 15px 7px 10px !important;line-height: 35px !important;height:51px !important;text-decoration: none !important;display:inline-flex !important;color:#ffffff !important;background-color:#5F7FFF !important;border-radius: 5px !important;border: 1px solid transparent !important;padding: 7px 15px 7px 10px !important;font-size: 22px !important;letter-spacing: 0.6px !important;box-shadow: 0px 1px 2px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;margin: 0 auto !important;font-family:'Cookie', cursive !important;-webkit-box-sizing: border-box !important;box-sizing: border-box !important;}.bmc-button:hover, .bmc-button:active, .bmc-button:focus {-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;text-decoration: none !important;box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;opacity: 0.85 !important;color:#ffffff !important;}</style><link href="https://fonts.googleapis.com/css?family=Cookie" rel="stylesheet"><a class="bmc-button" target="_blank" href="https://www.buymeacoffee.com/kip0d"><img src="https://cdn.buymeacoffee.com/buttons/bmc-new-btn-logo.svg" alt="Buy me a coffee"><span style="margin-left:5px;font-size:28px !important;">Buy me a coffee</span></a>