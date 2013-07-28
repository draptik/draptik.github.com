---
layout: post
title: "Unit testing RESTful services"
date: 2013-07-19 13:18
comments: true
categories: [java, RESTful, web service, testing]
---
In my two previous posts I gave an introduction on how to [consume a RESTful web service with AngularJS created by a Java backend](http://draptik.github.io/blog/2013/07/13/angularjs-example-using-a-java-restful-web-service/) and [use Guice in the Java backend](http://draptik.github.io/blog/2013/07/18/guice-in-java-web-application/).

In this post I will show how to create a unit test for this web service.

Most of this code is inspired by a [blog post from Paulo Renato de Athaydes](https://sites.google.com/a/athaydes.com/renato-athaydes//posts/jersey_guice_rest_api).

We will need to install some new dependencies:

- `jetty-maven-plugin`
- `junit`
- `jersey-client`
- `jersey-grizzly2`

[Grizzly](https://grizzly.java.net/) will be our web server for testing.

``` xml pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <name>ngdemo Maven Webapp</name>
    <groupId>ngdemo</groupId>
    <artifactId>ngdemo</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <jersey.version>1.17.1</jersey.version>
        <guice.version>3.0</guice.version>
    </properties>

    <build>
        <finalName>ngdemo</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.0</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.mortbay.jetty</groupId>
                <artifactId>jetty-maven-plugin</artifactId>
                <version>8.1.11.v20130520</version>
                <configuration>
                    <scanIntervalSeconds>10</scanIntervalSeconds>
                    <connectors>
                        <connector implementation="org.eclipse.jetty.nio.SelectChannelConnector">
                            <port>8080</port>
                            <maxIdleTime>60000</maxIdleTime>
                        </connector>
                    </connectors>
                    <stopKey/>
                    <stopPort/>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>

        <!-- javax: XML binding -->
        <dependency>
            <groupId>javax.xml.bind</groupId>
            <artifactId>jaxb-api</artifactId>
            <version>2.1</version>
        </dependency>

        <!-- RESTful web service: Jersey ====================================== -->
        <dependency>
            <groupId>com.sun.jersey</groupId>
            <artifactId>jersey-server</artifactId>
            <version>${jersey.version}</version>
        </dependency>
        <dependency>
            <groupId>com.sun.jersey</groupId>
            <artifactId>jersey-servlet</artifactId>
            <version>${jersey.version}</version>
        </dependency>
        <dependency>
            <groupId>com.sun.jersey</groupId>
            <artifactId>jersey-json</artifactId>
            <version>${jersey.version}</version>
        </dependency>

        <!-- Guice ============================================================= -->
        <dependency>
            <groupId>com.google.inject</groupId>
            <artifactId>guice</artifactId>
            <version>${guice.version}</version>
        </dependency>

        <dependency>
            <groupId>com.google.inject.extensions</groupId>
            <artifactId>guice-servlet</artifactId>
            <version>${guice.version}</version>
        </dependency>

        <dependency>
            <groupId>com.sun.jersey.contribs</groupId>
            <artifactId>jersey-guice</artifactId>
            <version>${jersey.version}</version>
        </dependency>

        <!-- Required for bypassing web.xml via Guice. Used in TestServlet.java -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>


        <!-- Unit testing ====================================================== -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>com.sun.jersey</groupId>
            <artifactId>jersey-client</artifactId>
            <version>${jersey.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>com.sun.jersey</groupId>
            <artifactId>jersey-grizzly2</artifactId>
            <version>${jersey.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```



Our class under test is `UserRestService.java`:

``` java UserRestService.java
package ngdemo.rest;

import com.google.inject.Inject;
import ngdemo.domain.User;
import ngdemo.service.contract.UserService;

import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;


@Path("/users")
public class UserRestService {

    private final UserService userService;

    @Inject
    public UserRestService(UserService userService) {
        this.userService = userService;
    }

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public User getDefaultUserInJSON() {
        return userService.getDefaultUser();
    }
}
```

Here is the corresponding unit test class `UserRestServiceTest.java`:

``` java UserRestServiceTest.java
package ngdemo.tests;

import com.google.inject.Guice;
import com.google.inject.Injector;
import com.google.inject.servlet.ServletModule;
import com.sun.jersey.api.client.Client;
import com.sun.jersey.api.client.ClientResponse;
import com.sun.jersey.api.client.WebResource;
import com.sun.jersey.api.client.config.DefaultClientConfig;
import com.sun.jersey.api.container.grizzly2.GrizzlyServerFactory;
import com.sun.jersey.api.core.PackagesResourceConfig;
import com.sun.jersey.api.core.ResourceConfig;
import com.sun.jersey.core.spi.component.ioc.IoCComponentProviderFactory;
import com.sun.jersey.guice.spi.container.GuiceComponentProviderFactory;
import ngdemo.repositories.contract.UserRepository;
import ngdemo.repositories.impl.UserRepositoryImpl;
import ngdemo.service.contract.UserService;
import ngdemo.service.impl.UserServiceImpl;
import org.glassfish.grizzly.http.server.HttpServer;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.UriBuilder;
import java.io.IOException;
import java.net.URI;

import static junit.framework.Assert.assertEquals;

public class UserRestServiceTest {

    static final URI BASE_URI = getBaseURI();
    HttpServer server;

    private static URI getBaseURI() {
        return UriBuilder.fromUri("http://localhost/").port(9998).build();
    }

    @Before
    public void startServer() throws IOException {
        System.out.println("Starting grizzly...");

        Injector injector = Guice.createInjector(new ServletModule() {
            @Override
            protected void configureServlets() {
                bind(UserService.class).to(UserServiceImpl.class);
                bind(UserRepository.class).to(UserRepositoryImpl.class);
            }
        });

        ResourceConfig rc = new PackagesResourceConfig("ngdemo.rest");
        IoCComponentProviderFactory ioc = new GuiceComponentProviderFactory(rc, injector);
        server = GrizzlyServerFactory.createHttpServer(BASE_URI + "rest/", rc, ioc);

        System.out.println(String.format("Jersey app started with WADL available at "
                + "%srest/application.wadl\nTry out %sngdemo\nHit enter to stop it...",
                BASE_URI, BASE_URI));
    }

    @After
    public void stopServer() {
        server.stop();
    }

    @Test
    public void testGetDefaultUser() throws IOException {
        Client client = Client.create(new DefaultClientConfig());
        WebResource service = client.resource(getBaseURI());
        ClientResponse resp = service.path("rest").path("users")
                .accept(MediaType.APPLICATION_JSON)
                .get(ClientResponse.class);
        System.out.println("Got stuff: " + resp);
        String text = resp.getEntity(String.class);

        assertEquals(200, resp.getStatus());
        assertEquals("{\"firstName\":\"JonFromREST\",\"lastName\":\"DoeFromREST\"}", text);
    }
}
```

In the `startServer` method we create an injector for Guice, which we can then pass into the `GuiceComponentProviderFactory` to create the inversion of control (IoC) container.

Together with the `ResourceConfig` the IoC container is passed to Grizzly's server factory to create the web server for testing.

Within the actual test method `testGetDefaultUser` we only have to setup the Jersey `Client` to retrieve the response (from the Grizzly server).

Here's the test output from Maven:

``` sh
$ mvn test
...
-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running ngdemo.tests.UserRestServiceTest
Starting grizzly...
Jul 19, 2013 1:50:35 PM com.sun.jersey.api.core.PackagesResourceConfig init
INFO: Scanning for root resource and provider classes in the packages:
  ngdemo.rest
Jul 19, 2013 1:50:35 PM com.sun.jersey.api.core.ScanningResourceConfig logClasses
INFO: Root resource classes found:
  class ngdemo.rest.UserRestService
Jul 19, 2013 1:50:35 PM com.sun.jersey.api.core.ScanningResourceConfig init
INFO: No provider classes found.
Jul 19, 2013 1:50:35 PM com.sun.jersey.server.impl.application.WebApplicationImpl _initiate
INFO: Initiating Jersey application, version 'Jersey: 1.17.1 02/28/2013 12:47 PM'
Jul 19, 2013 1:50:36 PM com.sun.jersey.guice.spi.container.GuiceComponentProviderFactory getComponentProvider
INFO: Binding ngdemo.rest.UserRestService to GuiceInstantiatedComponentProvider
Jul 19, 2013 1:50:37 PM org.glassfish.grizzly.http.server.NetworkListener start
INFO: Started listener bound to [localhost:9998]
Jul 19, 2013 1:50:37 PM org.glassfish.grizzly.http.server.HttpServer start
INFO: [HttpServer] Started.
Jersey app started with WADL available at http://localhost:9998/rest/application.wadl
Try out http://localhost:9998/ngdemo
Hit enter to stop it...
Got stuff: GET http://localhost:9998/rest/users returned a response status of 200 OK
Jul 19, 2013 1:50:37 PM org.glassfish.grizzly.http.server.NetworkListener stop
INFO: Stopped listener bound to [localhost:9998]
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 2.604 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 6.378s
```

Test time is 2.6 sec. Not bad considering we are starting a web server, deploying our app, creating a client, running the test and shutting down the web server.

Running this test from within IntelliJ takes: 0.009 sec...

You can clone a copy of this project here: [https://github.com/draptik/angulardemorestful](https://github.com/draptik/angulardemorestful).

To checkout the correct version for this demo, use the following code:

``` sh
git clone git@github.com:draptik/angulardemorestful.git
cd angulardemorestful
git checkout -f step3-backend-test
```

In case you are not using git you can also download the project as ZIP or tar.gz file here: [https://github.com/draptik/angulardemorestful/releases/tag/step3-backend-test](https://github.com/draptik/angulardemorestful/releases/tag/step3-backend-test)