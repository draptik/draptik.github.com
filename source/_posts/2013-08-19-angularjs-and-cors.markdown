---
layout: post
title: "AngularJS and CORS"
date: 2013-08-19 18:47
comments: true
categories: [RESTful, CORS, jsonp, web service, angularjs, javascript, java]
---

## TL;DR

While splitting my [AngularJS demo app](https://github.com/draptik/angulardemorestful) into independent back- and frontend projects (running two different servers) I stumbled across cross domain issues during development. This post describes how to implement CORS on the server and/or client side of an application.

## ...

This post describes how I split the backend and frontend of my [AngularJS demo app](https://github.com/draptik/angulardemorestful) into separate applications.

Hopefully this will simplify switching the used backend technology in the future (i.e. replacing Java with .NET or Node.JS).

## New directory structure

To begin with I created two new top level folders: `backend/java-backend` and `frontend`.
Then I moved all Java code (including Java IDE settings, pom.xml, etc.) to the new `java-backend` folder.
Since the frontend code (JS, CSS, HTML templates) was previously located in `src/main/webapp` I moved it to the new frontend folder.


The Java project folder `webapp` now only contains the following minimal setup:

``` sh
backend/java-backend/src/main/webapp
├── index.jsp
└── WEB-INF
    └── web.xml
```

The `frontend` folder now has the following (simplified) structure:

``` sh
frontend/
├── css
├── frontend-web-server.js
├── index.html
├── js
│   ├── app.js
│   ├── bootstrap
│   ├── controllers.js
│   ├── directives.js
│   ├── filters.js
│   ├── jquery
│   └── services.js
```

## Frontend web server 

Not knowing anything about node.js (yet), I just copied the simple [web server from Google's AngularJS demo application PhoneCat](https://github.com/angular/angular-phonecat/blob/master/scripts/web-server.js) to `frontend/frontend-web-server.js`. For this server to run you will have to install node.js on your system. The frontend server will be running on *port 8000*.

``` sh frontend-web-server.js
// ...
var DEFAULT_PORT = 8000; // <-- frontend port
// ...
```

We can start/stop the frontend web server using the scripts `start_frontend_server.sh` and `stop_frontend_server.sh`.

## Backend web server

The backend server is Tomcat. We can start/stop the backend web server using the scripts `start_java_backend.sh` and `stop_java_backend.sh`. The backend server will be running on *port 8080*.

## CORS configuration

Cross-origin resource sharing ([CORS](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing)) allows Javascript to make requests to other domains. Compared to JSONP which only allows the GET HTTP verb, CORS allows all HTTP verbs (GET, POST, PUT, DELETE), making it an ideal candidate for RESTful services. The only drawback: CORS requires a modern browser (see [Wikipedia for details](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing#Browser_support)). 

**2014-01-03**
~~CORS can be configured on the server and/or the client side.~~
CORS must be configured on the server **and** the client side (Thanks to Richard for the pointer!).
The following example demonstrates ~~both approaches~~ this.

### Server side configuration example (Java)

The basic idea is to add additional header information to the different `Access-Control-Allow-*` properties of the HTTP response.

``` java ResponseCorsFilter.java
package ngdemo.web.rest;

import com.google.inject.Singleton;

import javax.servlet.*;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/*
    Allow CORS requests.
 */
@Singleton
public class ResponseCorsFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException { }

    @Override
    public void destroy() { }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        if (servletResponse instanceof HttpServletResponse) {
            HttpServletResponse alteredResponse = ((HttpServletResponse) servletResponse);
            addHeadersFor200Response(alteredResponse);
        }
        filterChain.doFilter(servletRequest, servletResponse);
    }

    private void addHeadersFor200Response(HttpServletResponse response) {
        response.addHeader("Access-Control-Allow-Origin", "*");
        response.addHeader("Access-Control-Allow-Methods", "Cache-Control, Pragma, Origin, Authorization, Content-Type, X-Requested-With");
        response.addHeader("Access-Control-Allow-Headers", "GET, PUT, OPTIONS, X-XSRF-TOKEN");
    }
}
```

The above filter is used in the Guice configuration via the `filter(...).through(...)` method:

``` java NgDemoApplicationSetup.java
package ngdemo.infrastructure;

import com.google.inject.Guice;
import com.google.inject.Injector;
import com.google.inject.Scopes;
import com.google.inject.servlet.GuiceServletContextListener;
import com.google.inject.servlet.ServletModule;
import com.sun.jersey.api.core.PackagesResourceConfig;
import com.sun.jersey.api.core.ResourceConfig;
import com.sun.jersey.guice.spi.container.servlet.GuiceContainer;
import ngdemo.web.rest.ResponseCorsFilter;
import org.codehaus.jackson.jaxrs.JacksonJsonProvider;

public class NgDemoApplicationSetup extends GuiceServletContextListener {

    @Override
    protected Injector getInjector() {
        return Guice.createInjector(new ServletModule() {

            @Override
            protected void configureServlets() {
                super.configureServlets();
                ResourceConfig resourceConfig = new PackagesResourceConfig("ngdemo/web");
                for (Class<?> resource : resourceConfig.getClasses()) {
                    bind(resource);
                }
                bind(JacksonJsonProvider.class).in(Scopes.SINGLETON);
                serve("/web/*").with(GuiceContainer.class);

                // CORS filter:
                filter("/web/*").through(ResponseCorsFilter.class);
            }
        });
    }
}
```

### Client side configuration example (Javascript)

The opposite approach, configuring the client instead of the server, works by (1) setting the `useXDomain` property to true and (2) removing header properties.

``` javascript app.js
'use strict';

angular.module('ngdemo')
        .config(['$httpProvider', function ($httpProvider) {
        // ...

        // delete header from client:
        // http://stackoverflow.com/questions/17289195/angularjs-post-data-to-external-rest-api
        $httpProvider.defaults.useXDomain = true;
        delete $httpProvider.defaults.headers.common['X-Requested-With'];
    }]);
```

## Source code for this post

You can clone a copy of this project here: [https://github.com/draptik/angulardemorestful](https://github.com/draptik/angulardemorestful).

To checkout the correct version for this demo, use the following code:

``` sh
git clone git@github.com:draptik/angulardemorestful.git
cd angulardemorestful
git checkout -f step5-split-frontend-backend-cors
```

In case you are not using git you can also download the project as ZIP or tar.gz file here: [https://github.com/draptik/angulardemorestful/releases/tag/step5-split-frontend-backend-cors](https://github.com/draptik/angulardemorestful/releases/tag/step5-split-frontend-backend-cors)