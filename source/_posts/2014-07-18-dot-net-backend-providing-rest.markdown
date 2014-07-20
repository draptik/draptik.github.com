---
layout: post
title: ".NET backend providing REST"
date: 2014-07-18 18:46:33 +0200
comments: true
categories: [web service, c#, ioc, asp.net, web api, javascript, nodejs, CORS, angularjs, RESTful]
---

# TL;DR

My [AngularJS demo app](https://github.com/draptik/angulardemorestful) has a new backend implementation using [.NET Web API](http://www.asp.net/web-api).

# ...

## Recap

Our goals:

- server side: minimal working REST API providing
	- GET `dummy`
	- CRUD `users`
- client side (angular): communicate with server side


## Setup

Creating a Web API project is straightforward: Just follow the instructions at

- [Getting Started with ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
- [Enabling Cross-Origin Requests in ASP.NET Web API](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)
- [Dependency Injection for Web API Controllers](http://www.asp.net/web-api/overview/extensibility/using-the-web-api-dependency-resolver)

The final project structure will look like this:

{% img /images/posts/dotnet/dotnet-project-structure.PNG %}

## Adding Models

Create two new POCOs for `User` and `Dummy`:

``` csharp Models/Dummy.cs
namespace WebService.Models
{
    public class Dummy
    {
        public int Id { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
    }
}
```

``` csharp Models/User.cs
namespace WebService.Models
{
    public class User
    {
        public int Id { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
    }
}
```

## Adding Service Layer

Create a new folder `Service` and add a `UserService` with corresponding interface `IUserService`.

``` csharp Service/IUserService.cs
using System.Collections.Generic;
using WebService.Models;

namespace WebService.Service
{
    public interface IUserService
    {
        ICollection<User> GetAllUsers();
        User GetById(int userId);
        User UpdateUser(User user);
        User CreateNewUser(User user);
        void RemoveUserById(int userId);
    }
}
```

``` csharp Service/UserService.cs
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Linq;
using WebService.Models;

namespace WebService.Service
{
    public class UserService : IUserService
    {
        public UserService()
        {
            this.Users = new Collection<User>();
            this.CreateUsers();
        }

        private ICollection<User> Users { get; set; }

        public ICollection<User> GetAllUsers()
        {
            return this.Users;
        }

        public User GetById(int userId)
        {
            return this.Users.SingleOrDefault(x => x.Id.Equals(userId));
        }

        public User UpdateUser(User user)
        {
            var u = this.Users.SingleOrDefault(x => x.Id.Equals(user.Id));
            if (u != null) {
                u.FirstName = user.FirstName;
                u.LastName = user.LastName;
            }
            return u;
        }

        public User CreateNewUser(User user)
        {
            var newUser = new User
            {
                Id = this.Users.Max(x => x.Id) + 1,
                FirstName = user.FirstName,
                LastName = user.LastName
            };

            this.Users.Add(newUser);

            return newUser;
        }

        public void RemoveUserById(int userId)
        {
            this.Users.Remove(this.Users.SingleOrDefault(x => x.Id.Equals(userId)));
        }

        private void CreateUsers()
        {
            const int numberOfUsers = 10;
            for (int id = 1; id <= numberOfUsers; id++) {
                this.Users.Add(new User {Id = id, FirstName = "Foo" + id, LastName = "Bar" + id});
            }
        }
    }
}
```

Note: This is just a quick and dirty setup to get a working REST API without much overhead. In a real application the service will probably be a bit more fine grained. For example: In this demo app the users are simply stored in memory and not persisted to a database.


## Adding IoC for Web API

Add inversion of control (IoC) to Web API:

``` csharp IoC/UnityResolver.cs
using System;
using System.Collections.Generic;
using System.Web.Http.Dependencies;
using Microsoft.Practices.Unity;

namespace WebService.IoC
{
    /// <summary>
    /// http://www.asp.net/web-api/overview/extensibility/using-the-web-api-dependency-resolver
    /// </summary>
    public class UnityResolver : IDependencyResolver
    {
        private readonly IUnityContainer container;

        public UnityResolver(IUnityContainer container)
        {
            if (container == null) {
                throw new ArgumentNullException("container");
            }
            this.container = container;
        }

        public void Dispose()
        {
            this.container.Dispose();
        }

        public object GetService(Type serviceType)
        {
            try {
                return this.container.Resolve(serviceType);
            }
            catch (ResolutionFailedException) {
                return null;
            }
        }

        public IEnumerable<object> GetServices(Type serviceType)
        {
            try {
                return this.container.ResolveAll(serviceType);
            }
            catch (ResolutionFailedException) {
                return new List<object>();
            }
        }

        public IDependencyScope BeginScope()
        {
            var child = this.container.CreateChildContainer();
            return new UnityResolver(child);
        }
    }
}
```

## Adding Controllers

Create controllers `UsersController` and `DummyController`:

``` csharp Controllers/Dummy.cs
using System.Web.Http;
using System.Web.Http.Cors;
using WebService.Models;

namespace WebService.Controllers
{
    [EnableCors(origins: "http://localhost:9000", headers: "*", methods: "*")]
    public class DummyController : ApiController
    {
        public Dummy Get()
        {
            return new Dummy
            {
                Id = 0, 
                FirstName = "JonFromREST", 
                LastName = "Doe"
            };
        }
    }
}
```

``` csharp Controllers/UsersController.cs
using System.Collections.Generic;
using System.Web.Http;
using System.Web.Http.Cors;
using WebService.Models;
using WebService.Service;

namespace WebService.Controllers
{
    [EnableCors(origins: "http://localhost:9000", headers: "*", methods: "*")]
    public class UsersController : ApiController
    {
        private readonly IUserService userService;

        public UsersController(IUserService userService)
        {
            this.userService = userService;
        }

        public ICollection<User> Get()
        {
            return this.userService.GetAllUsers();
        }

        public User Get(int id)
        {
            return this.userService.GetById(id);
        }

        public User Put(User user)
        {
            return this.userService.UpdateUser(user);
        }

        public User Post(User user)
        {
            return this.userService.CreateNewUser(user);
        }

        public void Delete(int id)
        {
            this.userService.RemoveUserById(id);
        }
    }
}
```

### Putting the pieces together

Within `WebApiConfig.cs`:

- configure the IoC container
- activate CORS
- return JSON
- configure routes

``` csharp App_Start/WebApiConfig.cs
using System.Net.Http.Headers;
using System.Web.Http;
using Microsoft.Practices.Unity;
using WebService.IoC;
using WebService.Service;

namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // IoC container
            //
            // http://www.asp.net/web-api/overview/extensibility/using-the-web-api-dependency-resolver
            var container = new UnityContainer();
            // Note: for this demo we want the user service to be a singleton ('ContainerControlledLifetimeManager' in Unity syntax)
            container.RegisterType<IUserService, UserService>(new ContainerControlledLifetimeManager());
            config.DependencyResolver = new UnityResolver(container);

            // Web API configuration and services

            config.EnableCors();

            // Return JSON instead of XML http://stackoverflow.com/a/13277616/1062607
            config.Formatters.JsonFormatter.SupportedMediaTypes.Add(new MediaTypeHeaderValue("text/html"));

            // Web API routes
            config.MapHttpAttributeRoutes();

            const string baseUrl = "ngdemo/web";

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: baseUrl + "/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

## Returning Lower Case JSON from .NET Web API

C# uses upper case property names by default. JavaScript uses lower case property names by default.

To automatically convert between both worlds you can add a `ContractResolver` to your `Global.asax.cs`:

``` csharp Global.asax.cs
using System.Web;
using System.Web.Http;
using Newtonsoft.Json.Serialization;

namespace WebService
{
    public class WebApiApplication : HttpApplication
    {
        protected void Application_Start()
        {
            GlobalConfiguration.Configure(WebApiConfig.Register);

            // lower case property names in serialized JSON: http://stackoverflow.com/a/22130487/1062607
            GlobalConfiguration.Configuration
                .Formatters
                .JsonFormatter
                .SerializerSettings
                .ContractResolver = new CamelCasePropertyNamesContractResolver();
        }
    }
}
```

## Done?

Almost: For the ASP.NET backend to be reachable by the same URL as the other backends ([NodeJs backend](http://draptik.github.io/blog/2013/10/01/node-dot-js-backend-providing-rest/) and [Java backend](http://draptik.github.io/blog/2013/07/13/angularjs-example-using-a-java-restful-web-service/)) we have to change the default port of the application to 8080:

{% img /images/posts/dotnet/dotnet-project-url.PNG %}

Now we can start the Web API backend from Visual Studio (F5).

In the newly openend browser, check the URL [http:localhost:8080/nodedemo/web/dummy/](http:localhost:8080/nodedemo/web/dummy/). The dummy JSON object should be visible:

{% img /images/posts/dotnet/dotnet-project-browser00.PNG %}

## Check the API

### Start the backend

Start the Web API backend from Visual Studio.

### Start the frontend

Note: For setting up the frontend, you will need to install [NodeJS](http://nodejs.org/) and [Grunt](http://gruntjs.com/). Please have a look at the [README.md file in the frontend folder](https://github.com/draptik/angulardemorestful/blob/master/frontend/README.md) for further details.

Open a command prompt and navigate to the `frontend` folder.

Run `grunt server`.

``` sh
>grunt server
Running "server" task

Running "clean:server" (clean) task
Cleaning .tmp...OK

Running "concurrent:server" (concurrent) task

Running "coffee:dist" (coffee) task

Done, without errors.

Running "copy:styles" (copy) task


Done, without errors.

Running "compass:server" (compass) task
directory .tmp/styles/
       create .tmp/styles/main.css (1.718s)
    Compilation took 1.802s

Done, without errors.

Running "autoprefixer:dist" (autoprefixer) task
File ".tmp/styles/main.css" created.

Running "connect:livereload" (connect) task
Started connect web server on localhost:9000.

Running "open:server" (open) task

Running "watch" task
Waiting...
```

Visit URL [http://localhost:9000/#/dummy](http://localhost:9000/#/dummy):

{% img /images/posts/dotnet/dotnet-project-browser01.PNG %}

That's it.


## Source code for this post

You can clone a copy of this project here: [https://github.com/draptik/angulardemorestful](https://github.com/draptik/angulardemorestful).

To checkout the correct version for this demo, use the following code:

``` sh
git clone git@github.com:draptik/angulardemorestful.git
cd angulardemorestful
git checkout -f step7-aspnet-webapi-backend
```

In case you are not using git you can also download the project as ZIP or tar.gz file here: [https://github.com/draptik/angulardemorestful/releases/tag/step7-aspnet-webapi-backend](https://github.com/draptik/angulardemorestful/releases/tag/step7-aspnet-webapi-backend)