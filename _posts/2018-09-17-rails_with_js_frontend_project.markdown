---
layout: post
title:      "Rails with JS Frontend Project"
date:       2018-09-18 02:39:14 +0000
permalink:  rails_with_js_frontend_project
---

### jQuery to the rescue

This profect was super fun. I am now a full beliver in the fluidity and grace of jQuery. Having do all those xhr requests via XMLHttpRequest would have taken way longer, and been much more code. Here are a few things I grew to love about jQuery
1. grabbing attributes from a html element
```
let $form = $('#myForm')
$form.attr('action')
$form.data('userId')
```
-this helped me greatly when building ajax from forms, which brings me to number 2.
2. serializing form data

this is so cool, you need one function to serialize the entire form:
```
$form.serialize()
```
Amazing, it serializes it perfectly, and gets it prepared for sending.

3. showing and hiding elements on a page
```
$form.hide()
$form.show()
```
that's all it takes to update that elements style display attribute to none. Pretty sweet. 
4. And of course, the ease of AJAX gets and posts
```
$.get('/users', function(){
	}).done(function(response){
		console.log(response)
	})

$.ajax( {
	      type: "POST",
	      dataType: 'json',
	      url: $form.attr( 'action' ),
	      data: $form.serialize(),
	      success: function( response ) {
	        console.log(response)
	      }
	    } );
```
Great stuff. In my poject I used jQuery a ton, it becomes very fluid and second nature very quickly, great syntax in my opinion. Seems very snappy, requests send and load very fast. Makes alot of sense to use these types of requests as often as possible to keep apps quick, sending and recieving only the data that they need instead of entire page refreshes.

It changes my thoughts on app design. Be better to have as much on one page as possible, having all the interactivity using ajax gets and posts, and only redirecting/refreshing when necessary.

That is, until I learn React! Onwards... # Enter your title here

The content of your blog post goes here.
