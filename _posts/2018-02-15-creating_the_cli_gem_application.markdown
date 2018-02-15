---
layout: post
title:      "Creating the CLI Gem Application"
date:       2018-02-15 14:02:28 +0000
permalink:  creating_the_cli_gem_application
---


The Ruby CLI gem lab was a bit daunting at first. Create an executable CLI application, turn it into a gem, then... publish? Wait...like other people are going to see my chicken scratch code?? It felt like things were about to get real. 

I started with this crucial link: <a href="http://bundler.io/v1.12/guides/creating_gem.html">right here</a>. It actually begins so simply (just have to have bundler installed.) To start a gem, just type into your terminal: 

```
bundle gem my_first_gem
```

Now get ready for magic time. CD to your newly created folder and check out the goods. It creates an entire file structure for you, with a gemspec file, an rspec file, a license, version control, even a bin console environment! COOL!! Now all you have to do is...write your entire code for your project, no biggie.

I decided to create a app that scrapes LonelyPlanet for the top 10 travel destinations for 2018. I used the Nokogiri gem for the task. I initially had some trouble scraping their site, and learned a crucial lesson, *don’t try to scrape elements that are rendered via javascript or react!* The elements aren’t loaded until after the scrape is finished, so only returned nil. That only took me about 3 ½ hours of head scratching to figure out.

I created Classes for Countries, Cities, Regions, and Best Value destinations. Each Class would create 10 instances, which each had instance variables of a name, and a description. I initially wanted to scrape the entirety of the data as soon as the program loaded, but was taking too long (like 5-10 seconds of just frozen screen) so I decided to scrape the data as the user asked for it, seemed to be a bit smoother UX.

   The interface asks the user to choose a category, then a sub category, then returns a description and a link. I used another gem, colorize, to keep the interface color coded, a pretty cool gem for CLI apps. Seemed simple enough when I started, but took me about 8 hours, haha. Don’t get me wrong, it was super fun. and really rewarding in the end when everything loaded and worked. I definitely gained a newfound respect for complex applications, and the immense time and effort it takes to create them. 

  Thanks for reading!



