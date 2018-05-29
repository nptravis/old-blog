---
layout: post
title:      "My Rails Project"
date:       2018-05-29 12:44:11 +0000
permalink:  my_rails_project
---


### Student Profile Pages

I decided to make project that would interface with the popular educational software, PowerSchool, and add some special functionality that many schools ask for from the software. It basically displays student profiles, and acts as a dashboard for school administrators. It provides data visualizations for grades and behavior traits, and allow users to add comments as they review the students progress. I utilized a JavaScript chart library, Charts.js, in order to provide some nice dynamic and animated visual representations of data. 

I populated my database with real data provided by the school, that unfortunately I can not post any of the real data here, but you can get the idea how it would work. I seeded my database from a JSON file, but in the future hope to write and API for them to have their database serve up the data to a URL in real time, which will use my code written in my seed.rb file.

My models include Users, Students, Grades, Standards, and Courses. Here is an example of some validations and model methods, on my Students model:

```ruby
class Student < ActiveRecord::Base
	has_many :comments
	has_many :users, through: :comments
	has_many :grades
	has_many :standards, through: :grades
	has_many :courses, through: :grades
	validates :lastfirst, :student_number, :gradelevel, presence: true
	validates_uniqueness_of :student_number

	def uniq_courses
		self.courses.uniq
	end

	def self.search(word)
  	if word.present?
  		self.all.where('lastfirst LIKE ?', "%#{word}%")
  	else
  		self.all
  	end
  end

	def grades_per_semester(semester)
		self.grades.where("semester = ?", semester)
	end

	def grades_per_course(course)
		self.grades_per_semester("S1").where("course_id = ?", course.id)
	end

	def homs_per_course(course)
		arr = {}
		self.grades_per_course(course).each do |grade|
			if grade.standard.hom?
				arr[grade.standard.standardname] = grade.grade
			end
		end
		arr
	end

	def standards_per_course(course)
		arr = {}
		self.grades_per_course(course).each do |grade|
			if !grade.standard.hom?
				arr[grade.standard.standardname] = grade.grade
			end
		end
		arr
	end

end
```

As you can see I added some search functionality so users could easily find students. Basically, it's just a form (utilizing the form_tag ActionView::Helper method) that posts to the index action in the students_controller here:
```ruby
def index
		if params[:student]
     @students = Student.search(student_params[:search]).sort_by &:lastfirst
    else
     @students = Student.all.sort_by &:lastfirst
    end
	end
```
Simple but effective search function.

Also validations are to protect against any incomplete information, and to make sure the student_number was unique. You can also see some SQL relationships here, comments and grades act as join tables between users, standards, and courses.

I used some nested resources to allow me to post comments to a students show page, so nested comments under the student in order to have a url like this "/students/124/comments". And "/studnts/124/comment/new". Here is my nested route:
```ruby
resources :students do 
  	resources :comments
  end
```

I have basic login, logout, and signup functionality as well as the ability for users to login/signup with their existing google account using the OmniAuth gem (absolute lifesave, made the process so easy!). 

I had a little bit of trouble figuring out hwo to serve up my data from my models, to the front end so the Charts.js could work it's magic. I realize this part was a bit out of the scope of this project, but found a workaround by posting the data, in hash form, to a data attribute in a hidden div, using the content_tag helper:

```ruby
<%= content_tag :div, class: "student_list", id: "studentshow", data: {student: @s1_data_hash} do %>
<%end%>
```

Which then, I could grab that data hash with jQuery:

```javascript
$(document).on('turbolinks:load', function(){

var data_hash = $('#studentshow').data('student');

// all my charts.js code uses that data here

});
```

Make sure you wrap that JS code in a document on load function!

And then users can search the current students, see their grades in dynamic animated graphs, and post comments to share thouhts with other school administrators. 

This poject is still a work in progress, have learned a ton already, was super fun, looking forward to expand the functionality to include dynamic student schedules, and registration for after school programs. 

Thanks for reading!



