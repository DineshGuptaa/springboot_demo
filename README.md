---
tags: [spring-boot, test]
projects: [spring-boot]
---
spring_boot_version: 1.5.3.RELEASE
spring-boot: https://github.com/DineshGuptaa/springboot_demo
project_id: gs-spring-boot
This guide provides a sampling of how {spring-boot}[Spring Boot] helps you accelerate and facilitate application development. As you read more Spring Getting Started guides, you will see more use cases for Spring Boot.

== What you'll build
You'll build a simple web application with Spring Boot and add some useful services to it.

== What you'll need

java_version: 1.8

== Learn what you can do with Spring Boot

Spring Boot offers a fast way to build applications. It looks at your classpath and at beans you have configured, makes reasonable assumptions about what you're missing, and adds it. With Spring Boot you can focus more on business features and less on infrastructure.

For example:

- Got Spring MVC? There are several specific beans you almost always need, and Spring Boot adds them automatically. A Spring MVC app also needs a servlet container, so Spring Boot automatically configures embedded Tomcat.

== Create a simple web application
Now you can create a web controller for a simple web application.

`src/main/java/hello/HelloController.java`
[source,java]
----
include::initial/src/main/java/hello/HelloController.java[]
----
    
The class is flagged as a `@RestController`, meaning it's ready for use by Spring MVC to handle web requests. `@RequestMapping` maps `/` to the `index()` method. When invoked from a browser or using curl on the command line, the method returns pure text. That's because `@RestController` combines `@Controller` and `@ResponseBody`, two annotations that results in web requests returning data rather than a view.

== Create an Application class
Here you create an `Application` class with the components:

`src/main/java/hello/Application.java`
[source,java]
----
include::complete/src/main/java/hello/Application.java[]
----

There is also a `CommandLineRunner` method marked as a `@Bean` and this runs on start up. It retrieves all the beans that were created either by your app or were automatically added thanks to Spring Boot. It sorts them and prints them out.

== Run the application
To run the application, execute:
....
$ curl localhost:8080
Greetings from Spring Boot!


== Add Unit Tests

You will want to add a test for the endpoint you added, and Spring Test already provides some machinery for that, and it's easy to include in your project.


Now write a simple unit test that mocks the servlet request and response through your endpoint:

`src/test/java/hello/HelloControllerTest.java`
[source,java]
----
include::complete/src/test/java/hello/HelloControllerTest.java[]
----

The `MockMvc` comes from Spring Test and allows you, via a set of convenient builder classes, to send HTTP requests into the `DispatcherServlet` and make assertions about the result. Note the use of the `@AutoConfigureMockMvc` together with `@SpringBootTest` to inject a `MockMvc` instance. Having used `@SpringBootTest` we are asking for the whole application context to be created.  An alternative would be to ask Spring Boot to create only the web layers of the context using the `@WebMvcTest`. Spring Boot automatically tries to locate the main application class of your application in either case, but you can override it, or narrow it down, if you want to build something different.

As well as mocking the HTTP request cycle we can also use Spring Boot to write a very simple full-stack integration test. For example, instead of (or as well as) the mock test above we could do this:

`src/test/java/hello/HelloControllerIT.java`
[source,java]
----
complete/src/test/java/hello/HelloControllerIT.java[]
----

The embedded server is started up on a random port by virtue of the `webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT` and the actual port is discovered at runtime with the `@LocalServerPort`.

Add this to your build file's list of dependencies:

If you are using Maven, add this to your list of dependencies:

[source,xml]
----
include::complete/pom.xml[tag=actuator]
----

Then restart the app:

[subs="attributes"]
----
./gradlew build && java -jar build/libs/{project_id}-0.1.0.jar
----

If you are using Maven, execute:

[subs="attributes"]
----
mvn package && java -jar target/{project_id}-0.1.0.jar
----

You will see a new set of RESTful end points added to the application. These are management services provided by Spring Boot.


They include: errors, http://localhost:8080/env[environment], http://localhost:8080/health[health], http://localhost:8080/beans[beans], http://localhost:8080/info[info], http://localhost:8080/metrics[metrics], http://localhost:8080/trace[trace], http://localhost:8080/configprops[configprops], and http://localhost:8080/dump[dump].

NOTE: There is also a `/shutdown` endpoint, but it's only visible by default via JMX. To http://docs.spring.io/spring-boot/docs/{spring_boot_version}/reference/htmlsingle/#production-ready-customizing-endpoints[enable it as an HTTP endpoint], add 
`endpoints.shutdown.enabled=true` to your `application.properties` file.

It's easy to check the health of the app.

----
$ curl localhost:8080/health
{"status":"UP","diskSpace":{"status":"UP","total":397635555328,"free":328389529600,"threshold":10485760}}}
----

You can try to invoke shutdown through curl.

----
$ curl -X POST localhost:8080/shutdown
{"timestamp":1401820343710,"error":"Method Not Allowed","status":405,"message":"Request method 'POST' not supported"}
----

Because we didn't enable it, the request is blocked by the virtue of not existing.

From a different terminal window:
----
$ curl localhost:8080
Hello World!
----

== Summary
Congratulations! You built a simple web application with Spring Boot and learned how it can ramp up your development pace. You also turned on some handy production services.
This is only a small sampling of what Spring Boot can do. Checkout http://docs.spring.io/spring-boot/docs/{spring_boot_version}/reference/htmlsingle[Spring Boot's online docs]
if you want to dig deeper.
