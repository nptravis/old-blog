---
layout: post
title:      "Create A Sinatra Application from Scratch"
date:       2018-03-11 22:49:00 -0400
permalink:  create_a_sinatra_application_from_scratch
---


Just follow the steps!

1. In your terminal, cd to where you keep your projects, then create the main directory, then cd into it. In my example my main directory is named sinatra-project.
```
[code]$ mkdir sinatra-project
[code]$ cd sinatra-project
```

2. let's make some directories:
```
[sinatra-project]$ mkdir app
[sinatra-project]$ mkdir app/models
[sinatra-project]$ mkdir app/views
[sinatra-project]$ mkdir app/controller
[sinatra-project]$ mkdir config
[sinatra-project]$ mkdir db
[sinatra-project]$ mkdir public
[sinatra-project]$ mkdir spec
```

3. let's make some files:
```
[sinatra-project]$ touch app/controllers/application_controller.rb
[sinatra-project]$ touch app/views/layout.erb
[sinatra-project]$ touch app/views/index.erb
[sinatra-project]$ touch config/environment.rb
[sinatra-project]$ touch public/style.css
[sinatra-project]$ touch config.ru
[sinatra-project]$ touch Gemfile
[sinatra-project]$ touch Rakefile
[sinatra-project]$ touch LICENSE.md
[sinatra-project]$ echo "# An Awesome Sinatra Application" >> README.md
```

4. add version control
5. 

* First, have/create a github account, go to github.com/new and create your repository, initialize without a readme, as we already have that, and without gitignore or license, we already have license we you can add gitignore later, I named my repository sinatra-project.
* Then follow here:

```
git init
git add .
git commit -m "First Commit"
git remote add origin git@github.com:<your-username>/sinatra-project.git
git push -u origin master
```

5. Open up your project with your text editor of choice, mine is Sublime, so in terminal I simply type:

```
sublime .
```

6. Let's fill out some initial code, let's start with Gemfile, here are the gems I used, scope them out on rubygems.org to get an idea of what they do:
```
source 'http://rubygems.org'

gem 'sinatra'
gem 'activerecord', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
gem 'rake'
gem 'require_all'
gem 'rack-flash3'
gem 'sqlite3'
gem 'thin'
gem 'shotgun'
gem 'pry'
gem 'bcrypt'
gem "tux"

group :test do
  gem 'rspec'
  gem 'capybara'
  gem 'rack-test'
  gem 'database_cleaner', git: 'https://github.com/bmabey/database_cleaner.git'
end
```
-make sure at this point to run bundle install

7. Next, setup the environment.rb:
```
ENV['SINATRA_ENV'] ||= "development"

require 'bundler/setup'
Bundler.require(:default, ENV['SINATRA_ENV'])

ActiveRecord::Base.establish_connection(
  :adapter => "sqlite3",
  :database => "db/#{ENV['SINATRA_ENV']}.sqlite"
)

require_all 'app'

```
-This is setting Sinatra's initial environment to development, we can change this once the app is ready for production. Next, requires all your gems and tells bundler your sinatra env is development. Next creates a connection to your sqlite database, and last requires all your files in your app directory.

8. setup your config.ru:
```
require './config/environment'

if ActiveRecord::Migrator.needs_migration?
  raise 'Migrations are pending. Run `rake db:migrate` to resolve the issue.'
end

use Rack::MethodOverride
run ApplicationController
```
 -Here we are requiring our environment fil we just set up, raises a helpful warning is database migrations are pending, uses Rask Method Override middle ware for update and delete actions, and finally runs our application controller model. 

9. Next up, lets code out application_controller:
```
require './config/environment'

class ApplicationController < Sinatra::Base

  configure do
    set :public_folder, 'public'
    set :views, 'app/views'
    enable :sessions
  	set :session_secret, "secret"
  end

  get '/' do
  	erb :index
  end

end
```
-First we require our environemnt and then set up some basic configuration, importantly enabling sessions for users, then adding a get route action that loads our index page

10. Next, let's get our layout and index pages up:

here is the layout.erb:
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<link rel="stylesheet" type="text/css" href="<%= url("/style.css")%>">
	<title>Sinatra Project</title>
</head>
<body>
	<%= yield %>
	
</body>
</html>
```
-Note the use of the url method to load my css file, then drop the yield command. Sinatra will automatically look for a layout file, then layout will dynamically insert all other erb files asked for right where the yeild command is placed. (So you could put navigation, header, footer etc. into the layout so it would be the same for every page)

And here is index.erb:
```
<div class="main-content">
<h1>Welcome to the Home Page</h1>
<div>
```

11. Simple css styling just to make sure it is linked up correctly, drop this is style.css:
```
html, body {
	width: 100%;
	height: 100%;
}

h1 {
color: red
text-align: center;
}
```

12. Setup Rakefile, this is used to automate certain tasks, mostly I use it for databse migrations:
```
ENV["SINATRA_ENV"] ||= "development"

require_relative './config/environment'
require 'sinatra/activerecord/rake'

# Type `rake -T` on your command line to see the available rake tasks.
```

13. Check it out! Save all your files, then in the terminal start up the project by simply typing:
```
shotgun
```
-If everything is working, you will see something like this:
```
Listening on 127.0.0.1:9393
```
-Visit that address in the browser and see your page! Now, you can roll with it however you like, shotgun server will automatically update so you can see changes without having to restart the server, you can shut down shotgun with a simple crtl+c.

14. Lets make sure to git commit and push now, in your terminal:
```
git commit -am "Created basic template for project"
git push
```

Thanks for reading everyone, hope this helps to get your Sinatra projects up and running!!




