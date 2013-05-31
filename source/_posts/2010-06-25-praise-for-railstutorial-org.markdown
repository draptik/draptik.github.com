---
author: draptik
comments: true
date: 2010-06-25 20:56:42
layout: post
slug: praise-for-railstutorial-org
title: Praise for Railstutorial.org
wordpress_id: 333
categories:
- programming
---

A couple of weeks back I discovered a great tutorial on Ruby on Rails by Michael Hartl at ﻿﻿[http://www.railstutorial.org](http://www.railstutorial.org)

The tutorial takes you through the steps of building a [Twitter](http://twitter.com/)-like application (not a client!) from scratch. Aside from learning Ruby on Rails, this tutorial also teaches other useful technologies, techniques and best practices, such as:



	
  * [Behaviour driven development](http://en.wikipedia.org/wiki/Behavior_Driven_Development) (BDD) using [RSpec](http://rspec.info/) for unit, integration and functional testing

	
  * Version control using [Git](http://git-scm.com/)

	
  * Deployment using [Heroku](http://heroku.com/)

	
  * Basics of web design (CSS, Ajax)

	
  * Setting up a database (the tutorial uses the lightweight SQLite3 format, but this can be ported to 'real' databases quite easily with Rails)

	
  * Generating fake data for the test/staging database as well as the unit testing database


[sloccount](http://www.dwheeler.com/sloccount/) (SLOC: source lines of code) for the complete tutorial's source code:

``` sh
$ sloccount .
...
SLOC    Directory       SLOC-by-Language (Sorted)
1182    spec            ruby=1182
361     app             ruby=361
167     vendor          ruby=167
141     config          ruby=141
109     db              ruby=109
35      script          ruby=35
...
```

How to read these results?



	
  * app: The actual application is less than 400 lines of code (this even includes the generated HTML)! Try that using Java or ASP.NET... ;-)

	
  * spec: Tests are almost 4 times the size of the actual code (~1200 lines). These tests include unit, integration and functional tests!

	
  * Most of the remaining code is auto-generated Rails code.


You can get my commented sources from [GitHub](http://github.com) at [http://github.com/draptik/railstutorial](http://github.com/draptik/railstutorial)

If you want to have a look at the application: It is currently deployed at [Heroku](http://heroku.com/): [http://draptik-railstutorial.heroku.com/](http://draptik-railstutorial.heroku.com/)
