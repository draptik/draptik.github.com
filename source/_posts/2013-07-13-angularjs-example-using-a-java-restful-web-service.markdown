---
layout: post
title: "AngularJS example using a Java RESTful web service"
date: 2013-07-13 20:12
comments: true
categories: [java, jersey, javascript, angularjs]
---

[AngularJS](http://angularjs.org/) is the current MVV-Whatever JavaScript framework by Google. 
Among other things, it provides bidirectional data binding.

Although I'm neither a Java nor a JavaScript expert, I choose the following scenario for my 'Hello-World' example:

1. Java backend provides a RESTful web service.

2. AngularJS consumes the web service.

That's it.


# Project structure

I intentionally put the backend and frontend code in the same project to simplify the example. In a real project you probably want to have seperate projects for front- and backend.

``` sh
+---------------------------------------------------+
| demo project                                      |
|                                                   |
| +----------------+              +---------------+ |
| | backend (Java) | < -(REST)- > | frontend (JS) | |
| +----------------+              +---------------+ |
|                                                   |
+---------------------------------------------------+
```


Since the backend is Java based, I used a Maven default structure (`maven-archetype-site-simple`):


``` sh project structure
├── _documentation
│   └── readme.txt
├── ngdemo.iml
├── pom.xml
└── src
    └── main
        ├── java
        │   └── ngdemo
        │       ├── domain
        │       │   └── User.java
        │       ├── rest
        │       │   └── UserRestService.java
        │       └── service
        │           └── UserService.java
        └── webapp
            ├── css
            │   └── app.css
            ├── img
            ├── index-async.html
            ├── index.html
            ├── index.jsp
            ├── js
            │   ├── app.js
            │   ├── controllers.js
            │   ├── directives.js
            │   ├── filters.js
            │   └── services.js
            ├── lib
            │   └── angular
            │       ├── angular-cookies.js
            │       ├── angular-cookies.min.js
            │       ├── angular.js
            │       ├── angular-loader.js
            │       ├── angular-loader.min.js
            │       ├── angular.min.js
            │       ├── angular-resource.js
            │       ├── angular-resource.min.js
            │       ├── angular-sanitize.js
            │       ├── angular-sanitize.min.js
            │       └── version.txt
            ├── partials
            │   └── partial1.html
            └── WEB-INF
                └── web.xml
```

`src/main/java` is the backend.

`src/main/webapp/js` is the frontend.

`src/main/webapp/` also includes a copy of angular-seed.


# RESTful web service (backend)

[Jersey](https://jersey.java.net/) is the Java reference implementation for providing REST.

Install the following dependencies in your `pom.xml`:

``` xml pom.xml
<!-- .. -->
<!-- RESTful web service: Jersey -->
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-server</artifactId>
    <version>1.17.1</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-servlet</artifactId>
    <version>1.17.1</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-json</artifactId>
    <version>1.17.1</version>
</dependency>
<!-- .. -->
```

Add the following servlet snippet to your `web.xml`:

``` html web.xml
<!-- .. -->
<servlet>
    <servlet-name>jersey-serlvet</servlet-name>

    <servlet-class>
        com.sun.jersey.spi.container.servlet.ServletContainer
    </servlet-class>

    <init-param>
        <param-name>com.sun.jersey.config.property.packages</param-name>
        <param-value>ngdemo.rest</param-value>
    </init-param>

    <init-param>
        <param-name>com.sun.jersey.api.json.POJOMappingFeature</param-name>
        <param-value>true</param-value>
    </init-param>

    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>jersey-serlvet</servlet-name>
    <url-pattern>/rest/*</url-pattern>
</servlet-mapping>
<!-- .. -->
```

Enough configuration for now: Create a simple `User` object...

``` java User.java
package ngdemo.domain;

import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement
public class User {

    private String firstName;
    private String lastName;

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
}
```

...and a service class...

``` java UserService.java
package ngdemo.service;

import ngdemo.domain.User;

public class UserService {

    public User getDefaultUser() {
        User user = new User();
        user.setFirstName("JonFromREST");
        user.setLastName("DoeFromREST");
        return user;
    }
}
```

...and finally the RESTful Service...:

``` java UserRestService.java
package ngdemo.rest;

import ngdemo.domain.User;
import ngdemo.service.UserService;
import ngdemo.service.UserServiceImpl;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;


@Path("/users")
public class UserRestService {

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public User getDefaultUserInJSON() {
        UserService userService = new UserServiceImpl();
        return userService.getDefaultUser();
    }
}
```

Converting the `User` object to JSON via `@Produces(MediaType.APPLICATION_JSON)` requires jersey-json in `web.xml` (`POJOMappingFeature`).

# Consuming web service from AngularJS (frontend)

Don't forget to add `angular-resources.js` to your `index.html`...


Consuming the web service:

``` javascript services.js
var services = angular.module('ngdemo.services', ['ngResource']);

services.factory('UserFactory', function ($resource) {
    return $resource('/ngdemo/rest/users', {}, {
        query: {
            method: 'GET',
            params: {},
            isArray: false
        }
    })
});
```

Usage in controller:

``` javascript controller.js
var app = angular.module('ngdemo.controllers', []);

app.controller('MyCtrl1', ['$scope', 'UserFactory', function ($scope, UserFactory) {
    UserFactory.get({}, function (userFactory) {
        $scope.firstname = userFactory.firstName;
    })
}]);
```

Usage in view:

{% codeblock %}
{% raw %}
<div>
    <p>
        Result from RESTful service is: {{ firstname }}
    </p>
</div>
{% endraw %}
{% endcodeblock %}

Et voila:

{% img /images/posts/angular/browser_screenshot.png %}

Update (2013-07-18):

You can clone a copy of this project here: [https://github.com/draptik/angulardemorestful](https://github.com/draptik/angulardemorestful).

To checkout the correct version for this demo, use the following code:

``` sh
git clone git@github.com:draptik/angulardemorestful.git
cd angulardemorestful
git checkout -f step1
```
