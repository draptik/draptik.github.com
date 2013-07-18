---
layout: post
title: "Guice in Java Web Application"
date: 2013-07-18 21:36
comments: true
categories: [java, guice, ioc]
---
[Google's Guice framework](http://en.wikipedia.org/wiki/Google_Guice) promises to be a lightweight(!) [Inversion-of-Control](http://en.wikipedia.org/wiki/Inversion_of_control) (IoC) container.

Advantages compared to [Spring](http://en.wikipedia.org/wiki/Spring_Framework):

- Spring is much more than an IoC container, and therefore overkill for many projects.
- Configuration by code. *NO XML*.

Based on my [previous post showing how to use AngularJS with a Java RESTful backend](http://draptik.github.io/blog/2013/07/13/angularjs-example-using-a-java-restful-web-service/) I extended the simple demo application to use Guice.

Let's say we have a `UserServiceImpl` class which depends on a `UserFactory` interface. The `UserFactory` interface is injected into the constructor of the `UserServiceImpl` class.

The only thing we have to do is add the `@Inject` annotation to the constructor so that Guice can do its job.


``` java UserServiceImpl.java
package ngdemo.service.impl;

import com.google.inject.Inject;
import com.google.inject.Singleton;
import ngdemo.domain.User;
import ngdemo.service.contract.UserFactory;
import ngdemo.service.contract.UserService;

import java.util.List;

public class UserServiceImpl implements UserService {

    private final UserFactory userFactory;

    @Inject
    public UserServiceImpl(UserFactory userFactory) {
        this.userFactory = userFactory;
    }

    @Override
    public List<User> getDefaultUsers() {
        return this.userFactory.createUsers();
    }

    @Override
    public User getDefaultUser() {
        return this.userFactory.createUser();
    }

}
```
For the IoC container to know which implementation to inject we have to create a Guice *Module* which derives from `AbstractModule`:

``` java UserModule.java
package ngdemo.infrastructure;

import com.google.inject.AbstractModule;
import ngdemo.service.contract.UserFactory;
import ngdemo.service.contract.UserService;
import ngdemo.service.impl.UserFactoryImpl;
import ngdemo.service.impl.UserServiceImpl;

public class UserModule extends AbstractModule {
    @Override
    protected void configure() {
        bind(UserFactory.class).to(UserFactoryImpl.class);
        bind(UserService.class).to(UserServiceImpl.class);
    }
}
```

The `UserModule` class demonstrates the advantage of Guice vs. Spring: *NO XML*. When using Spring you normally would have to create Spring beans in an XML file like this:

``` xml applicationContext.xml (pseudo code)
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

	<bean id="userService" class="ngdemo.service.impl.UserServiceImpl">
		<constructor-arg ref="userFactory"/>
	</bean>
	
	<bean id="userFactory" class="ngdemo.service.impl.UserFactoryImpl" />
		
</beans>
```

Next we have to create a replacement for the servlets required by the servlet container:

``` java ngdemo.infrastructure.NgDemoApplicationSetup.java
package ngdemo.infrastructure;

import com.google.inject.Guice;
import com.google.inject.Injector;
import com.google.inject.servlet.GuiceServletContextListener;
import com.google.inject.servlet.ServletModule;
import com.sun.jersey.api.core.PackagesResourceConfig;
import com.sun.jersey.api.core.ResourceConfig;
import com.sun.jersey.guice.spi.container.servlet.GuiceContainer;

public class NgDemoApplicationSetup extends GuiceServletContextListener {

    @Override
    protected Injector getInjector() {

        return Guice.createInjector(new ServletModule() {

            @Override
            protected void configureServlets() {

                super.configureServlets();

                // Configuring Jersey via Guice:
                ResourceConfig resourceConfig = new PackagesResourceConfig("ngdemo/rest");
                for (Class<?> resource : resourceConfig.getClasses()) {
                    bind(resource);
                }
                serve("/rest/*").with(GuiceContainer.class);
            }
        }, new UserModule()); // <-- Adding other Guice Dependency Injection Modules
    }
}
```

And finally the file `web.xml`:

``` xml web.xml 
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app id="WebApp_ID" version="2.4"
         xmlns="http://java.sun.com/xml/ns/j2ee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee
	http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">

    <display-name>Restful Web Application</display-name>

    <filter>
        <filter-name>guiceFilter</filter-name>
        <filter-class>com.google.inject.servlet.GuiceFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>guiceFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <listener>
        <listener-class>ngdemo.infrastructure.NgDemoApplicationSetup</listener-class>
    </listener>
</web-app>
```
The file `web.xml` is now free of any `<servlet>` tags. The only thing that has to be configured in XML is the `<listener-class>`. The value of the `<listener-class>` is our Java class `NgDemoApplicationSetup`, so all further configuration can be defined in a type safe manner.

You can clone a copy of this project here: [https://github.com/draptik/angulardemorestful](https://github.com/draptik/angulardemorestful).

To checkout the correct version for this demo, use the following code:

``` sh
git clone git@github.com:draptik/angulardemorestful.git
cd angulardemorestful
git checkout -f step2-guice
```

