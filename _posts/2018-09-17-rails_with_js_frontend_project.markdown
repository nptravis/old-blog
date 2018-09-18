---
layout: post
title:      "Rails with JS Frontend Project"
date:       2018-09-17 22:39:15 -0400
permalink:  rails_with_js_frontend_project
---


### jQuery to the rescue

This project was super fun. I am now a full believer in the fluidity and grace of jQuery. Having to do all those xhr requests via XMLHttpRequest would have taken way longer, and been much more code. Here are a few things I grew to love about jQuery
1. grabbing attributes from a html element
```
let $form = $('#myForm')
$form.attr('action')
$form.data('userId')
```
-this helped me greatly when building ajax requests from forms, which brings me to number 2.

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

4. And of course, the ease of AJAX gets and posts. 

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

Great stuff. In my project I used jQuery a ton, it becomes very fluid and second nature very quickly, great syntax in my opinion. Seems very snappy, requests send and load very fast. Makes alot of sense to use these types of requests as often as possible to keep apps quick, sending and recieving only the data that they need instead of entire page refreshes.

It changes my thoughts on app design. Be better to have as much on one page as possible, having all the interactivity using ajax gets and posts, and only redirecting/refreshing when necessary.

That is, until I learn React...

### Active Model Serializer

This project really opened my eyes to how serializing works. Basically, the internet is all strings flying around. No objects or variables are sent anywhere, they are all converted into strings before being sent. Now, I made many mistakes while understanding serialization, one was sending un-serialized json only to discover it was just one long string! No keys or values. The other was relying to much on to_json. It workd great for simple things, like serializing one model, with one association/method, but i never got the systax correct for adding methods and associations. So, i finally dug into Active Model Serializer, and it's awesome.
Here are things I love:
1. remove unwanted attributes from models you don't need to send:
```class StudentSerializer < ApplicationSerializer
  attributes :id
end
```
That will only send ids, which was all I needed, saved alot of time in my request/response cycle. 
2. easliy attach associations just like you would with a model:
```
class CommentSerializer < ApplicationSerializer
	attributes :content, :id
	belongs_to :user, serializer: UserSerializer
	belongs_to :student, serializer: StudentSerializer
end
```
this adds the association, but like before, it is customized with it's own serializer
3. serialize an entire show view
```
class TeacherSerializer < ApplicationSerializer
	attributes :email, :id, :lastfirst, :show
	attributes :current_sections

	has_many :sections
	has_many :students, through: :sections

	def show
		TeachersController.render(:show, assigns: {teacher: object}, layout: false)
	end

	def current_sections
		TeachersController.render(:current_sections, assigns: {teacher: object}, layout: false).squish
	end
```
the show method attached here is rendering the entire shotw.html.erb for the teacher model. Serialized the whole thing, and now I can plop it wherever I want, I dont think that is even possible with to_json.
Big fan of this pattern.

## Conclusion

Learned a ton with this project. Really feel more comfortable with jQuery, serialization, and rails in general. Was alot of work, but alot of fun. Now on to React, onwards and upwards!

