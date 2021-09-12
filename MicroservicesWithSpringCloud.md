# **Microservices using Spring Boot and Spring Cloud**

### **What is a Microservice?**
A Microservices is REST enabled, small well chosen deployable units that are cloud enabled.

### **Challenges building our microservices**
**1. Bounded Context - To identify what you should and shouldn't do in each of the microservices**

Deciding the boundaries of microservices is an evolutionary process and it is not something we get it right 
the first time. We need to play around with, follow domain driven design and then try to identify the right 
boundary for a microservice based on the knowledge you have at that time.

**2. Configuration Management**

We can have 5 or 50 plus microservices with multiple instances in each of the environments. For instance, let's
say there are 10 microservices, 5 environments and 50 instances. That's a lot of work for the operations team
to maintain.

**3. Dynamic Scale Up and Scale Down** 

Establishing the technology to be able to do the loads on different microservices will be different at 
different instances of time and at a particular instance, one might need 2 instances of microservice-2. But 
at a different point in time one might be needing 10 instances of it. So I should be able to bring the new
instances of Microservices up and bring down older instances of Microservices when they are not really needed 
with a dynamic load balancing because when there is 1 instance of microservice-1 and there are 4 instances of
microservice-2, then one would want to distribute the load between all the instances of microservice-2 and if 
there are 4 instances of microservice-2 coming up then I would want to ensure that all the new ones are also 
being used to the fullest extent. So we need the ability to dynamically bring in new instances and also to 
distribute the load among the new instance.
 
**4. Visibility & Monitoring** 

If I say the functionality is now distributed among 10 microservices and there is a bug. How do you identify 
where the bug is? You need to have a centralized log where you can go and find out what happened for a specific
request which MicroService caused the problem. Not just that. We also need monitoring around these microservices
because we may have hundreds of microservices, we need to be able to identify the microservices which are down. 
We would want to be able to automatically identify those where there is not enough disk space. All these kind of
things need to be automated. So we need great visibility into what's happening with these microservices.
 
**5. Pack Of Cards**

And last but not the least important of them is the fact that if it's not a well-designed Microservices architecture, 
then it can be like a pack of cards. What do I mean? It's basically that in Microservices architecture, you have 
one Microservice calling another and another calling another. So there could be a certain Microservice which would 
be fundamental for the whole chain of Microservices. If in case, the Microservice goes down then the entire application
might go down. So it's like a pack of cards. You're building one over the top of the other and so on and so forth. 
Therefore they collapse very easily and therefore it's very important for you to have fault tolerance in your
Microservices.

### **Intro to Spring Cloud**

There are a various components under the umbrella of Spring Cloud which help us to provide solutions to the above
challenges. Spring Cloud provides tools to quickly build some of the common patterns in distributed systems to 
the typical problems which are present for distributed systems in the cloud. Spring Cloud provides a range of
solutions. The most important thing that you need to understand is, Spring Cloud is not really one project 
as such. There are a wide variety of projects under the umbrella of Spring Cloud and some of them are,

* Spring Cloud Config: Provides a centralized configuration management
* Spring Cloud Bus: This enables the microservices and the infrastructure components things like config server 
and API gateway to talk to each other.

Now let's take a quick look at the challenges which we discussed earlier and the projects in Spring Cloud that
provide solutions to those challenges. 

### Challenge #2: Configuration Management:

We talked about the fact that there would be multiple microservices, multiple 
environments and for each of these microservices there would be multiple instances in many of those environments. 
This would mean that there is a lot of configuration for these microservices that the operations team needs to 
manage.

***Spring Cloud Config Server*** provides an approach where you can store all configuration for all the different
environment of all the Microservices in a Git repository. So you can store all the configuration for different
environments of different Microservices in just one place, in a centralized location and Spring Cloud Config Server
can be used to expose those configurations to all the Microservices. This helps us to keep the configuration in 
one place and that makes it very easy to maintain the configuration for all the MicroServices.

### Challenge #3: Dynamic Scale Up and Scale Down:

For instance, assume you have a MicroService called CurrencyCalculationService which is talking to the
CurrencyExchangeService. Assume there are multiple instances of the CurrencyExchangeService and it's possible 
that at any point in time new instances can be added in or removed out and we would want the CurrencyCalculationService
to be able to distribute the load between all the instances of the CurrencyExchangeService and to be able to
dynamically check about all the available instances of the CurrencyExchangeService and make sure that the load
is distributed among all of them.

The solution for this would be using a ***naming server*** called as ***Eureka***. So all the instances of all
MicroServices would register with this naming server. 

The naming server has two important features.

* **Service Registration** - All microservices can register with the microservices.
* **Service Discovery** -  In the above example, the CurrencyCalculationService can ask the Eureka naming server, 
***"Hey naming server, give me the current instances of CurrencyExchangeService"*** and the naming service would 
provide those URLs to the currency calculations. This helps to establish dynamic relationship between the
CurrencyCalculationService and the instances of the CurrencyExchangeService. We use ***Ribbon*** for client 
side load balancing in older versions of Spring Cloud. In recent releases, we use ***Spring Cloud LoadBalancer***.

What this means is, **CurrencyCalculationService** will host Ribbon/Spring Cloud LoadBalancer and it would make sure
that the load is evenly distributed among the existing instances that it gets from the naming server. We'll also use 
***Feign*** in the CurrencyCalculationService as a mechanism to write simple RESTful clients.

### Challenge #4: Visibility and Monitoring:
The solutions for visibility and monitoring are the ***Zipkin Distributing Tracing Server***. We would use 
***Spring Cloud Sleuth*** to assign **_ID_** to request across multiple components and we would use the Zipkin 
distributed tracing to trace a request across multiple components.

One of the important things about microservices is these microservices have a lot of common features. For example,
logging security analytics. You don't want to implement all these common features in every microservice.

API Gateways provide great solutions to these kind of challenges. We will use a ***Netflix Zuul API Gateway***.

We implement _**Fault Tolerance**_ using ***Hystrix*** if a service is down. Hystrix help us to configure a default
response.

### Microservices - V2 - What's new
* Latest version of Spring Boot and Spring Cloud
    * Spring Cloud LoadBalancer instead of Ribbon.
    * Spring Cloud API Gateway instead of Zuul.
    * Resilience4J instead of Hystrix as a Circuit Breaker.
* Docker: Containerize Microservices
    * Run Microservices using Docker and DockerCompose.
* Kubernetes: Orchestrate all our Microservices with Kubernetes

### Setting up config server
Dependency to use is

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

* Inside our application.properties file, we need to have this configuration

```properties
spring.config.import=optional:configserver:http://localhost:8888
```

Here we are saying, we have a config server but it is optional. With earlier versions of Spring Boot, this 
config server was not needed until the time we had the config server ready, but with the latest versions of 
Spring Boot, the applications do not even startup if you have Spring Cloud Starter Config dependency in our
Microservices.
* The three ways to fetch values from our configuration file. 
    * We can hardcode our configurations in our code. 
    * Read them from a application.properties file.
    * Connect to a Git repository having configurations in a properties file.
* The values in _**application.properties**_ will have a less priority compared to the values present in your Git 
repository.
* The name of the application (limits-service) must match the name of the properties file, 
**_limits-service.properties_**, in the Git repository.
* Also, make sure to have a proper path to the config server:
**spring.config.import=optional:configserver:http://localhost:8888**
* We need to enable config server by using the annotation **@EnableConfigServer** in our SpringCloudConfigServer 
project.
* We need to specify the application name from where we would like to read the values from properties file.

```properties
spring.application.name=spring-cloud-config-server
```

### How to store configuration related to multiple environments in a GitHub repository?
We would create multiple copies of properties file for multiple environments in our config server project like - 

* For DEV - **limits-service-dev.properties**
* For QA - **limits-service-qa.properties**
* For DEFAULT - **limits-service.properties**

To pick up the configuration from DEV, we would hit

```text
http://localhost:8888/limits-service/dev
```

To pick up the configuration from QA, we would hit

```text
http://localhost:8888/limits-service/qa
```

To pick up the configuration from DEFAULT, we would hit

```text
http://localhost:8888/limits-service/default
```

These would also return the default limits-service.properties file but the values which are configured
in limits-service-dev and limits-service-qa will have a higher priority than the values configured in
limits-service properties file.

The way you would actually configure this in code is by using **spring.profiles** in application.properties 
file.

To configure properties for other **Microservices-X** and **Microservices-Y**, we would just create properties 
files with the name that correspond to the project Microservices-X and Microservices-Y. 
For instance, **microservices-X.properties** and **microservices-Y.properties**. The advantage of this is we 
will have all the properties of all the environments centralized.

We could create **microservices-X-dev.properties** for dev environment and **microservices-X-qa.properties** 
for QA environment. The operations team can control all configuration related to all Microservices for multiple 
environments in a single location and that's it. The change would be ready and distributed to multiple 
environments.

### Introduction to CurrencyExchangeMicroservice and CurrencyConversionMicroservice

**CurrencyConversionMicroservice -> CurrencyExchangeMicroservice -> Database**

CurrencyConversionMicroservice is all about converting 10 USD to INR. From here, we would be exposing a simple
URL.

Endpoint:

***/currency-conversion/from/USD/to/INR/quantity/10***

Response:

```json
{
    "id": 10001,
    "from": "USD",
    "to": "INR",
    "conversionMultiple": 74.00,
    "quantity": 10,
    "totalCalculatedAmount": 740.00,
    "environment": "8000 instance-id" 
}
```

CurrencyExchangeMicroservice is about knowing the exchange rate from one currency to another. From here, we would be
exposing a simple URL.

Endpoint:

***/currency-exchange/from/USD/to/INR***

Response:

```json
{
    "id": 10001,
    "from": "USD",
    "to": "INR",
    "conversionMultiple": 74.00,
    "environment": "8000 instance-id"
}
```

In our use case, CurrencyConversionMicroservice would call the CurrencyExchangeMicroservice to get the value of
USD in INR today. The return value would be multiplied with the quantity and give us the response back.

### **Using Feign REST Client for Service Invocation**
Feign makes it really, really easy to call other Microservices. To make use of Feign we need to add the below
dependency.

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

* The first thing we would want to do is we would want to be able to talk to the **CurrencyExchangeService** from the
**CurrencyConversionResource**.
* To be able to do that what we need to do is to create a proxy. This proxy should be an _"interface"_.
* The next thing that we'd need to do is to annotate **CurrencyConversionServiceApplication** main class with the
annotation **@EnableFeignClients**.
* Next thing is to create a proxy named **CurrencyExchangeProxy** and annotate it with **@FeignClient**. Typically, we
would name this with the application name of the service that we would want to call in here. In this example, it
would be named as ***currency-exchange*** which is present in the application.properties file of 
CurrencyExchangeMicroservice. 
* The other thing is to specify the _"url"_ of the currency-exchange. We need to only specify the domain and not the
whole url as shown below. The url to be called should be part of the method definition.

```java
@FeignClient(name = "currency-exchange", url = "localhost:8000")
public interface CurrencyExchangeProxy {

  @GetMapping("/currency-exchange/from/{from}/to/{to}")
  CurrencyConversion retrieveExchangeValue(
      @PathVariable String from, @PathVariable String to);
}
```

* We can copy the method definition from the CurrencyExchangeResource class and change the return type from
CurrencyExchange to CurrencyConversion and since the CurrencyConversion bean matches the structure of the response of
the CurrencyExchange and therefore the return values are automatically mapped into the CurrencyConversion bean. 
* Now that the CurrencyExchangeProxy is ready, we would want to make use of the CurrencyExchangeProxy in our
CurrencyConversionResource.

```java
@RestController
public class CurrencyConversionResource {

  @Autowired
  CurrencyExchangeProxy currencyExchangeProxy;

  @GetMapping("/currency-conversion-feign/from/{from}/to/{to}/quantity/{quantity}")
  public CurrencyConversion calculateCurrencyConversionFeign(
     @PathVariable String from, @PathVariable String to, @PathVariable BigDecimal quantity) {
     CurrencyConversion currencyConversion = currencyExchangeProxy.retrieveExchangeValue(from, to);
     
     return new CurrencyConversion(
        currencyConversion.getId(),
          from,
          to,
          quantity,
          currencyConversion.getConversionMultiple(),
          quantity.multiply(currencyConversion.getConversionMultiple()),
          currencyConversion.getEnvironment());
  }
}
```

* When we have the naming server in picture, Feign also helps us to do load balancing very easily.

### **What is Naming Server/Service Registry and how to set up Eureka Naming Server**
We got the entire chain working - CurrencyConversionMicroservice, CurrencyExchangeMicroservice and talking to 
and in-memory database. However, there is still a small problem. What is the problem? 

In the CurrencyExchangeProxy, we are hard coding the url of the CurrencyExchangeMicroservice. If I would
want to get the CurrencyConversionMicroservice to talk to a different instance of CurrencyExchangeMicroservice we 
need to manually go to proxy and change it. 

The state we would want to be able to get to is we would want to dynamically launch currency exchange instances and
distribute load between them. As instances come up and go down, we want to be able to automatically discover them and
load balance between them. Let's say Feign provided an option where you can hardcode multiple URL's, even that
would not be a good solution because let's say 8000 went down and let's say a new instance was brought up on 8000. 
Then you have to change the configuration of this application of the code all the time and that's the reason why we
go for something called a Service Registry or a Naming Server.

What would happen in a Microservices architecture is, all the instances of all the Microservices would register with
a Service Registry. In our case, both CurrencyConversionMicroservice and CurrencyExchangeMicroservice would register
with the service registry along with the other Microservices. 

Let's say, CurrencyConversionMicroservice wants to talk to the CurrencyExchangeMicroservice. It would ask the
service registry, "_**Hey, what are the addresses of the CurrencyExchangeMicroservice?**_".

The service registry would return those back to the CurrencyConversionMicroservice and then the
CurrencyConversionMicroservice can send the request out to the CurrencyExchangeMicroservice. All the instances would
register with the naming server or the service registry and whenever CurrencyConversionMicroservice wants to find
out what are the active instances, it asks the naming server, gets to the instances and load balances between them.
One of the options that Spring Cloud provides is ***Eureka Naming Server***.

### **Registering CurrencyConversionMicroservice and CurrencyExchangeMicroservice with the naming server**
In order to register any Microservice all we need to do is to have the below dependency in the pom.xml file.

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

This would automatically register any Microservice with the Eureka Naming Server. Default port of Eureka Naming Server 
is _***8761***_. Each time we restart the Microservice, it would unregister and then it would register again.

### **Load Balancing with Eureka, Feign and Spring Cloud LoadBalancer**
All that we need to do to load balance between multiple instances of CurrencyExchangeMicroservice is actually remove
the url attribute from the annotation @FeignClient in our CurrencyExchangeProxy interface.

```java
@FeignClient(name = "currency-exchange")
public interface CurrencyExchangeProxy {

  @GetMapping("/currency-exchange/from/{from}/to/{to}")
  CurrencyConversion retrieveExchangeValue(
      @PathVariable String from, @PathVariable String to);
}
```

What we would want to do is to want the Feign client to talk to Eureka and pick up the instances of 
CurrencyExchangeMicroservice and do load balancing between them.

What happens is, inside the CurrencyConversionMicroservice, there is a load balancer component which is talking to
the naming server, finding the instances, and doing automatic load balancing between them. This is what is called
**Client-Side Load Balancing**.

The **SpringCloudStarterNetflixEurekaClient** would bring in **SpringCloudStarterLoadbalancer** JAR into the
classpath and this is the load balancer framework that is used by Feign to actually distribute the load among the 
multiple instances which are returned by Eureka. In the earlier versions of Spring Cloud the load balancer used 
was ***Ribbon*** and in the recent versions, Spring Cloud shifted to using Spring Cloud LoadBalancer as the load
balancer. The great thing is, if you're using Eureka and Feign, then load balancing comes for free.

### **Setting up Spring Cloud API Gateway**
We implemented CurrencyConversionMicroservice, CurrencyExchangeMicroservice, LimitsMicroservice and in typical 
Microservices architecture, there would be hundreds of Microservices like these and these Microservices have a lot of
common features - authentication, authorization, logging, rate limiting. Where do you implement all these common
features? That's one of the typical questions in a Microservices architecture and the typical solution is to go for
an **API Gateway**.

In the older versions of Spring Cloud, the popular API gateway to use was _**Zuul**_. Since Zuul 1 is no longer
supported by Netflix stack, Spring Cloud has moved on and now the recommended option as an API gateway is **Spring Cloud
API Gateway**.

This API Gateway would be the one hosting the _**Eureka Discovery Client dependency - A REST based service for locating
services for the purpose of load balancing and failover of middle-tier servers.**_ Because of the below dependency, API
Gateway would automatically register itself with Eureka.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

* Since this is the API Gateway, we would need to add the _**Spring Cloud Routing (Gateway) - which provides a simple, 
yet effective way to route to APIs and provide cross cutting concerns to them such as security, monitoring/metrics, 
and resiliency.**_

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

* Also, we need to add a property inside application.properties and register Spring Cloud API Gateway with the Eureka
Naming Server. This is not a necessary step, but advisable.

```properties
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka
```

### What is Spring Cloud API Gateway and why it is needed?
Let's suppose you have 20 services to which user can interact to, and since we do not want to expose each and 
every services publicly as that will be madness (because all services will have different ports and context) we will
use an API gateway which will act as single entry point access to our application (developed in Microservice pattern) 
and that is where Spring Cloud API Gateway comes into picture. Spring Cloud API Gateway act as a reverse proxy to
all your Microservices running behind it and is capable of providing the following services.

* Authentication
* Dynamic Routing
* Service Migration
* Load Shedding
* Security
* Static Response handling
* Active/Active traffic management

### API Gateway URLs
The microservices CurrencyConversionService and CurrencyExchangeService are registered with Eureka Naming Server with 
the names CURRENCY-CONVERSION and CURRENCY-EXCHANGE respectively.

To access CurrencyConversionService, the URL would be,

* Using RestTemplate
```text
http://localhost:8765/CURRENCY-CONVERSION/currency-conversion/from/USD/to/INR/quantity/10
```

* Using Feign Client
```text
http://localhost:8765/CURRENCY-CONVERSION/currency-conversion-feign/from/USD/to/INR/quantity/10
```

To access CurrencyExchangeService, the URL would be,

```text
http://localhost:8765/CURRENCY-EXCHANGE/currency-exchange/from/USD/to/INR
```

What we are doing here is we are passing in the name that currency exchange is registered with in here and we'd want 
the Spring Cloud API Gateway to talk to Eureka Naming Server with that name, find the server location, and then execute 
requests to this URL. 

At this point it's not enabled yet. To enable this feature in Spring Cloud API Gateway, we need to add in a property
to our application.properties file in api-gateway Microservice. Since API Gateway is already registered with Eureka 
Naming Server, it will enable it using the the discovery client.

```properties
spring.cloud.gateway.discovery.locator.enabled=true
```

So, now whenever you have a client for the Currency Exchange, we can actually give this URL. Once you implement all
the common features in your API gateway and the API Gateway would take care of the common features and then invoke 
the CurrencyExchangeMicroservice.

This is how you call any Microservice which is registered with Eureka Naming Server through the Spring Cloud API
Gateway. 

Now, if you'd want to implement things like authentication, you can implement them on API Gateway and you can only 
allow those things which are authenticated in your Microservices. So, all the authentication logic can be implemented 
on the API gateway. 

Now, one of the problems that we see in here is these URLs does not really look good.

```text
http://localhost:8765/CURRENCY-CONVERSION/currency-conversion/from/USD/to/INR/quantity/10
http://localhost:8765/CURRENCY-CONVERSION/currency-conversion-feign/from/USD/to/INR/quantity/10
http://localhost:8765/CURRENCY-EXCHANGE/currency-exchange/from/USD/to/INR
```

The way we can fix this is by having the below property in application.properties file.

```properties
spring.cloud.gateway.discovery.locator.lower-case-service-id=true
```

Once this is done, the above URLs will not be functional. We can now use the URLs with small cases.

```text
http://localhost:8765/currency-conversion/currency-conversion/from/USD/to/INR/quantity/10
http://localhost:8765/currency-conversion/currency-conversion-feign/from/USD/to/INR/quantity/10
http://localhost:8765/currency-exchange/currency-exchange/from/USD/to/INR
```

### Exploring Custom Routes with Spring Cloud API Gateway
One of the options to build custom routes is to actually create a configuration file in our api-gateway Microservice. We
can use the Microservice to add in all custom routes to our application. We can map a request to a specific URL. The URL
can be a URL to our Microservices with authentication headers which are being added in.

Here, we can redirect the requests coming to 

```text
http://localhost:8765/currency-exchange/currency-exchange/from/USD/to/INR
```

to redirect back to the below URL by configuring a custom route.

```text
http://localhost:8765/currency-exchange/from/USD/to/INR
```

For the custom configuration to work, we need to comment out the below properties added to out api-gateway.

#### should be commented out to provide our own custom configuration via a configuration class.
```properties
spring.cloud.gateway.discovery.locator.enabled=true
spring.cloud.gateway.discovery.locator.lower-case-service-id=true
```

In order to have the custom configuration, we have the following.

```java
@Configuration
public class ApiGatewayConfiguration {

  @Bean
  public RouteLocator gatewayRouter(RouteLocatorBuilder routeLocatorBuilder) {
    return routeLocatorBuilder
        .routes()
        .route(
            predicateSpec ->
                predicateSpec.path("/currency-exchange/**").uri("lb://currency-exchange"))
        .build();
  }
}
```

What we are saying here is, if we get a request to a URL that matches the regular expression "/currency-exchange/**",
then we would want to redirect using the naming server and I would also want to do load balancing. In, "lb://currency
-exchange", currency-exchange is the name of the registration on the Eureka server.

Also, we can rewrite a new path and make it to redirect to an existing URL in the naming server.

```java
@Configuration
public class ApiGatewayConfiguration {

  @Bean
  public RouteLocator gatewayRouter(RouteLocatorBuilder routeLocatorBuilder) {
    return routeLocatorBuilder
        .routes()
        .route(
            predicateSpec ->
                predicateSpec
                    .path("/get")
                    .filters(
                        gatewayFilterSpec ->
                            gatewayFilterSpec.addRequestHeader("MyHeader", "MyURI"))
                    .uri("http://httpbin.org:80"))
        .route(
            predicateSpec ->
                predicateSpec.path("/currency-exchange/**").uri("lb://currency-exchange"))
        .route(
            predicateSpec ->
                predicateSpec.path("/currency-conversion/**").uri("lb://currency-conversion"))
        .route(
            predicateSpec ->
                predicateSpec.path("/currency-conversion-feign/**").uri("lb://currency-conversion"))
        .route(
            predicateSpec ->
                predicateSpec
                    .path("/currency-conversion-new/**")
                    .filters(
                        gatewayFilterSpec ->
                            gatewayFilterSpec.rewritePath(
                                "/currency-conversion-new/(?<segment>.*)",
                                "/currency-conversion-feign/${segment}"))
                    .uri("lb://currency-conversion"))
        .build();
  }
}
```

### Implementing Spring Cloud Gateway Logging Filter
As we saw actually how to add custom filters on specific paths, in API gateway we can also add global filters. Let's
say, I would want to log every request that goes through the API gateway. For this, we need to create a new filter.

```java
public class LoggingFilter implements GlobalFilter {

  @Override
  public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
    return null;
  }
}
```

**GlobalFilter** is what we would need to implement to be able to do something and then do the execution of the request.

```java
@Slf4j
@Component
public class LoggingFilter implements GlobalFilter {

  @Override
  public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
    log.info("Path of the request received: " + exchange.getRequest().getPath());
    return chain.filter(exchange);
  }
}
```

Once the filter is in place, any request that comes through the naming server will be logged in. You can log other 
information about the request like authentication for all the requests in this place.

#### Spring Cloud Gateway Summary:
* Simple, yet effective way to route to APIs.
* Provide cross cutting concerns:
    * Security
    * Monitoring/metrics
* Built on top of Spring WebFlux (Reactive approach)
* Features:
    *  Match routes on any request attribute not just path. We can match based on headers, host, request method, query
    parameter etc.
    * Define predicates (using path()) and filters (using filters()).
    * Integrates with Spring Cloud Discovery Client (Load Balancing).
    * Path Rewriting.
*  Working: We define a handler mapping and based on the handler mapping, the appropriate handler is called. And as 
part of the handler, we have a number of filters which would be executed and finally, the request would be sent out 
to the service, which needs to handle that request. And when the response comes back, you can also do a lot of things 
with it and you can send it back to the client.

### Getting started with Circuit Breaker - Resilience4j
Until now, we have talked about the fact that in a Microservices architecture, there is a complex call chain. 

Microservice1 --> Microservice2 --> Microservice3 --> Microservice4 --> Microservice5

As shown in the example here, a Microservice can call another Microservice, that Microservice might be dependent on 
another Microservice, and so on and so forth. What would happen if one of these services is down or is very slow?
Let's say Microservice4 is down or it's very, very slow. What would happen? There would be an impact on the entire
chain. Even if it's slow, then there is a corresponding impact on the other microservices too. In these microservices, 
there will be a build-up of calls because Microservice4 is slow, all the other Microservice in the chain also get
impacted.

####Questions:
* Can we return a **fallback response** if a service is down? That is, if I see that the Microservice4 is down, then in the 
Microservice3, can I return a fallback response? Can I configure a default response? This might not always be possible.
For example, in the case of a credit card transaction or something of that kind, you do not have any fallback responses 
possible. But in the case of a shopping application, instead of returning a set of products, you might return a default 
set of products; that's possible.

* Can we implement a **Circuit Breaker Pattern** to reduce the load? If I see that Microservice4 is down, instead of 
repeatedly hitting it and causing it to go down, can I actually return the default response back without even hitting 
the Microservice? Now, there are a lot of nuances to the Circuit Breaker pattern.

* Can we retry requests in case of temporary failures? If there is a temporary failure from a Microservice4, can I
retry it a few times? and only when it has failed multiple times, I return a default response back?

* Can we implement **Rate Limiting**? I'd want to allow only a certain number of calls to a specific microservice in a 
specific period of time. 

In Spring Boot, there is a Circuit Breaker framework which is available, which is called **Resilience4j**. Resilience4j 
is a lightweight, easy-to-use fault tolerant library inspired by Netflix Hystrix. In the previous versions of Spring
Boot and Spring Cloud, **Netflix Hystrix** was the recommended Circuit Breaker framework. However, with the evolution
of Java 8 and functional programming, Resilience4j has become the recommended framework.

The module expects Spring Boot Actuator and AOP are already provided at runtime.

### Playing with Resiliency4j - Retry and Fallback methods

#### **Retry**
Let's say a Microservice which we are calling is down temporarily and sometimes you know that if you invoke the same 
thing multiple times, it might give you a response back. In those kinds of situations, you can go for a retry.

```java
@RestController
public class CircuitBreakerResource {

  @GetMapping("/sample-api")
  @Retry(name = "sample-api")
  public String sampleAPI() {
    ResponseEntity<String> dummyEntity =
        new RestTemplate().getForEntity("http://localhost:8080/some-dummy-url", String.class);
    return dummyEntity.getBody();
  }
}
```

Here, we are using the default retry configuration and when we try to hit the end point, the logs would be written as 
shown below.

```text
2021-09-05 15:07:14.952  INFO 18344 --- [nio-8000-exec-1] i.p.m.c.resource.CircuitBreakerResource  : Sample API call received
2021-09-05 15:07:15.512  INFO 18344 --- [nio-8000-exec-1] i.p.m.c.resource.CircuitBreakerResource  : Sample API call received
2021-09-05 15:07:16.168  INFO 18344 --- [nio-8000-exec-1] i.p.m.c.resource.CircuitBreakerResource  : Sample API call received
```

So, by default, **@Retry**, what it does is if there is any failure or exception during the execution of the
specific method, it would be retried thrice. If the retry fails all the three times, only then it would return an
error back.

To configure a specific number of retry intervals do is we need to create our own specific retry configuration in the 
application.properties file.

```properties
resilience4j.retry.instances.sample-api.max-attempts=5
```

Now, @Retry would try to hit the end point 5 times.

```text
2021-09-05 15:17:51.290  INFO 27848 --- [nio-8000-exec-1] i.p.m.c.resource.CircuitBreakerResource  : Sample API call received
2021-09-05 15:17:51.843  INFO 27848 --- [nio-8000-exec-1] i.p.m.c.resource.CircuitBreakerResource  : Sample API call received
2021-09-05 15:17:52.357  INFO 27848 --- [nio-8000-exec-1] i.p.m.c.resource.CircuitBreakerResource  : Sample API call received
2021-09-05 15:17:52.875  INFO 27848 --- [nio-8000-exec-1] i.p.m.c.resource.CircuitBreakerResource  : Sample API call received
2021-09-05 15:17:53.407  INFO 27848 --- [nio-8000-exec-1] i.p.m.c.resource.CircuitBreakerResource  : Sample API call received
```

#### **Fallback**
Another thing we can do actually is configure a fallback method using which we can configure a response. The fallback
method should accept an Exception argument. We can have different fallback methods for different kinds of exceptions.

```java
@RestController
@Slf4j
public class CircuitBreakerResource {

  @GetMapping("/sample-api")
  @Retry(name = "sample-api", fallbackMethod = "hardcodedResponse")
  public String sampleAPI() {
    log.info("Sample API call received");
    ResponseEntity<String> dummyEntity =
        new RestTemplate().getForEntity("http://localhost:8080/some-dummy-url", String.class);
    return dummyEntity.getBody();
  }

  public String hardcodedResponse(Exception ex) {
    return "Fallback response";
  }
}
```

Here, 5 retries will happen and at the end of 5 retries if the API call was still not successful, then it would
resort to the custom fallback response.

We can customize with intervals between retries. So, if an API call is failing, it would wait for a little while and
then make the API call again.

```properties
resilience4j.retry.instances.sample-api.wait-duration=1s
```

The other thing you can also configure is exponential backoff. 

```properties
resilience4j.retry.instances.sample-api.enable-exponential-backoff=true
```

#### What is exponential backoff?
After we enable this property each request would take a little bit more time because each subsequent request, it
would wait for a little longer. This is what is called as an exponential backoff. 

So, whenever we are working on a Cloud, for example AWS, and you are working with some API, most of the APIs use 
exponential backoff. So, if you are calling an API and the first attempt is failing, what it would do is it would make
the next attempt after, let's say, 1 second. The subsequent attempt will be made after 2 seconds. The attempt after 
that would be made after 4, 8, 16 seconds and so on increasing exponentially. 

However, what if the service is really down for a long time? That's the kind of scenarios where we would go for a 
**Circuit Breaker pattern**.

### Playing with Circuit Breaker features of Resilience4j
Instead of @Retry, we need to use another annotation called @CircuitBreaker.

```java
@RestController
@Slf4j
public class CircuitBreakerResource {

  @GetMapping("/sample-api")
  @CircuitBreaker(name = "default", fallbackMethod = "hardcodedResponse")
  public String sampleAPI() {
    log.info("Sample API call received");
    ResponseEntity<String> dummyEntity =
        new RestTemplate().getForEntity("http://localhost:8080/some-dummy-url", String.class);
    return dummyEntity.getBody();
  }

  public String hardcodedResponse(Exception ex) {
    return "Fallback response";
  }
}
```

#### What is the magic that @CircuitBreaker really brings in?
Now, to see the magic of CircuitBreaker, what we would need to do is to fire a lot of requests to 
`http://localhost:8000/sample-api` API. From windows, we should try firing in 100-200 quick requests from the browser
by quickly refreshing.

Once we do this, what you would see right when you go back to the console is that the CircuitBreaker Sample api is being 
called up to a certain extent and after that, you'd see that there are requests going on, however, you'd see that there 
are no method calls in the console. The CircuitBreaker is returning the response back without even calling the end point
method. That means the method is not being called, directly the fallback method is being called and the response is 
returned back. However, you can fire up the requests as many times as you want. This is what the CircuitBreaker framework
allows us to do.

So, if a Microservice is down, the CircuitBreaker gets to know that all the requests to a particular Microservice
failing. So instead of calling the method and adding load to it, it would instead return a default response back
directly. So, the circuit breaker will break the circuit and it will directly return a response back. 

_**Now, you might be wondering, how do I know if the microservice is back up and I can call it again?**_ To be able
to answer that, we'd need to understand how a CircuitBreaker works.

In the official documentation, you would see there are multiple stages for a CircuitBreaker. The CircuitBreaker can
be in 3 different states.

* CLOSED
* OPEN
* HALF_OPEN

##### CLOSED
In a closed state, the CircuitBreaker will always be calling the dependent Microservice.

##### OPEN
In an OPEN state, the CircuitBreaker will not call the dependent Microservice, it would directly return the fallback 
response.

##### HALF_OPEN
In a HALF_OPEN state, a CircuitBreaker would be sending a percentage of requests to the dependent Microservice and for 
rest of the requests, it would return the hardcoded response or the fallback response back. 

### When does a CircuitBreaker switch from one state to another?
For example, when you start the application up, the CircuitBreaker is in the CLOSED state. Now, let's say, I'm calling 
the dependent Microservice 10000 times and I see that all of them are failing or I see that 90% of them are failing. 
In that kind of scenario, the CircuitBreaker would switch to an OPEN state. Once it switches to OPEN state, it waits 
for a little while; there's a wait duration that you can configure. After that wait duration, the CircuitBreaker would 
switch to a HALF_OPEN state. During the HALF_OPEN state, the CircuitBreaker would try and see if the dependent 
Microservice is up. So, it sends a percentage of the request, you can configure how much percentage it would send, 
let's say, 10% or 20% of the requests to the dependent Microservice, and if it gets proper responses for that, then it 
would go back to the CLOSED state and if it does not get proper responses, then it would go back to the OPEN state.

In [Resilience4j documentation](https://resilience4j.readme.io/docs/getting-started-3), we have a wide range of things 
that we can configure on the CircuitBreaker in our Spring Boot application.

### Exploring Rate Limiting and BulkHead Features of Resilience4j
#### Rate Limiting
Basically, Rate Limiting is all about saying, in 10 seconds, I'd want to only allow 10000 calls to our API. We are
setting a time period and during that time period, I only want to allow a specific number of calls and you are 
configuring it to a specific API. For all the API methods present, you can set different rate limits. For other APIs
in the same Microservice, you can have different names and you can configure different limits for them.

```java
@RestController
@Slf4j
public class CircuitBreakerResource {

  @GetMapping("/sample-api")
  @CircuitBreaker(name = "default", fallbackMethod = "hardcodedResponse")
  @RateLimiter(name = "default")
  public String sampleAPI() {
    log.info("Sample API call received");
    ResponseEntity<String> dummyEntity =
        new RestTemplate().getForEntity("http://localhost:8080/some-dummy-url", String.class);
    return dummyEntity.getBody();
  }

  public String hardcodedResponse(Exception ex) {
    return "Fallback response";
  }
}
```

Now, how can you configure these limits?
The way you can do that is by going to the application.properties and as expected, it would be Resilience4j. What this
below properties tell us is, we would want to make 2 requests every 10 seconds.

```properties
resilience4j.ratelimiter.instances.default.limitForPeriod=2
resilience4j.ratelimiter.instances.default.limit-refresh-period=10s
```

With this configuration, you would see the "default" RateLimiter does not permit further calls if the calls exceed 2
requests in the 10 seconds window. The RateLimiter would cut the service off. 

#### BulkHead
In addition to RateLimiter, you can also configure how many concurrent calls are allowed and that's called as BulkHead.

```java
@RestController
@Slf4j
public class CircuitBreakerResource {

  @GetMapping("/sample-api")
  @BulkHead(name = "sample-api")
  public String sampleAPI() {
    log.info("Sample API call received");
    ResponseEntity<String> dummyEntity =
        new RestTemplate().getForEntity("http://localhost:8080/some-dummy-url", String.class);
    return dummyEntity.getBody();
  }

  public String hardcodedResponse(Exception ex) {
    return "Fallback response";
  }
}
```

For each of the APIs inside a Microservice, you can configure a BulkHead using the below property.

```properties
resilience4j.bulkhead.instances.sample-api.max-concurrent-calls=10
```
