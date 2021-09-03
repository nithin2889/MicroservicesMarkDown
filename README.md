## **Microservices using Spring Boot and Spring Cloud**

**What is a Microservice?**

A Microservices is REST enabled, small well chosen deployable units that are cloud enabled.

### **Challenges building our microservices**

Challenges:
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

And last but not the least important of them is the fact that if it's not a well-designed microservices architecture, 
then it can be like a pack of cards. What do I mean? It's basically that in microservices architecture. You have 
one microservice calling another and another calling another. So there would be certain microservices which would 
be fundamental for the whole thing. If in case, the microservice goes down then the entire application might go 
down. So it's like a pack of cards. You're building one over the top of the other and so on and so forth. Therefore
they collapse very easily and therefore it's very important for you to have fault tolerance in your microservices.

### **Intro to Spring Cloud**

There are a various components under the umbrella of Spring Cloud which help us to provide solutions to the above
challenges. Spring Cloud provides tools to quickly build some of the common patterns in distributed systems to 
the typical problems which are present for distributed systems in the cloud. Spring Cloud provides a range of
solutions. The most important thing that you need to understand is, Spring Cloud is not really one project 
as such. There are a wide variety of projects under the umbrella of Spring Cloud. 

There are a wide range of projects under the umbrella of Spring Cloud. Some of them are,

* Spring Cloud Config: Provides a centralized configuration management
* Spring Cloud Bus: This enables the microservices and the infrastructure components things like config server 
things like API gateway to talk to each other.

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
that at any point in time new instances can be added in or removed out and we would want the currency calculations
that we'd want to be able to distribute the load between all the instances of the CurrencyExchangeService who 
would want to be able to dynamically check what are the available instances of the currency exchange service and 
make sure that the load is distributed among all of them.

The solution for this would be using a ***naming server*** called as ***Eureka***. So all the instances of all
MicroServices would register with this naming server. 

The naming server has two important features.

* Service Registration - All microservices can register with the microservices.
* Service Discovery -  In the above example, the CurrencyCalculationService can ask the Eureka naming server, 
***"Hey naming server, give me the current instances of CurrencyExchangeService"*** and the naming service would 
provide those URLs to the currency calculations. This helps to establish dynamic relationship between the
CurrencyCalculationService and the instances of the CurrencyExchangeService. We will use ***Ribbon*** for client 
side load balancing in older versions of Spring Cloud and use ***Spring Cloud LoadBalancer*** That means the
CurrencyCalculationService will host Ribbon and it would make sure that the load is evenly distributed among the
existing instances that it gets from the naming server. We'll also use ***Feign*** in the CurrencyCalculationService
as a mechanism to write simple RESTful clients. 

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
    * Spring Cloud Gateway instead of Zuul.
    * Resilience4J instead of Hystrix as a Circuit Breaker.
* Docker: Containerize Microservices
    * Run Microservices using Docker and DockerCompose.
* Kubernetes: Orchestrate all our Microservices with Kubernetes

### Setting up config server
Inside our application.properties file, we would have this configuration

* Here we are saying, we have a config server but it is optional. With earlier versions of Spring Boot this 
config server was not needed until the time we had the config server ready but with the latest versions of 
Spring Boot, the applications do not even startup if you have Spring Cloud Starter Config dependency in our
Microservices.
* We have three ways to fetch values from our configuration file. 
    * We can hardcode our configurations in our code. 
    * Read them from a application.properties file.
    * Connect to a Git repository having configurations in a properties file.
* The values in _**application.properties**_ will have a less priority compared to the values present in your Git 
repository.
* The name of the application (limits-service) must match the name of the properties file, 
**_limits-service.properties_**, in the Git repository.
* Also, make sure to have a proper path to the config server:
**spring.config.import=optional:configserver:http://localhost:8888**
* We need to enable config server by using the annotation @EnableConfigServer in our SpringCloudConfigServer 
project.
* We need to specify the application name from where we would like to read the values from properties file.
**spring.application.name=spring-cloud-config-server**

### How to store configuration related to multiple environments in a GitHub repository?
We would create multiple copies of properties file for multiple environments in our config server project like - 

* For DEV - limits-service-dev.properties
* For QA - limits-service-qa.properties
* For DEFAULT - limits-service.properties

To pick up the configuration from DEV, we would hit the URL - http://localhost:8888/limits-service/dev
To pick up the configuration from QA, we would hit the URL - http://localhost:8888/limits-service/qa
To pick up the configuration from DEFAULT, we would hit the URL - http://localhost:8888/limits-service/default

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

CurrencyExchangeMicroservice is all about what the exchange rate of one currency to another. From here, we would
be exposing a simple URL.

Endpoint:

***/currency-exchange/from/USD/to/INR***

Response:
```
{
    "id": 10001,
    "from": "USD",
    "to": "INR",
    "conversionMultiple": 74.00,
    "environment": "8000 instance-id"
}
```

CurrencyConversionMicroservice is all about converting 10 USD to INR. From here, we would be exposing a simple
URL.

Endpoint:

***/currency-conversion/from/USD/to/INR/quantity/10***

Response:
```
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

In our use case, CurrencyConversionMicroservice would call the CurrencyExchangeMicroservice to get the value of
USD in INR today. The return value would be multiplied with the quantity and give us the response back.

### **Using Feign REST Client for Service Invocation**
Feign makes it really, really easy to call other Microservices. To make use of Feign we need to add the below
dependency.

```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

* The first thing we would want to do is we would want to be able to talk to the CurrencyExchangeService from the
CurrencyConversionResource, and to be able to do that what we need to do is to create a proxy. This proxy should
be an interface.
* The second thing that we'd need to do is to annotate CurrencyConversionServiceApplication main class with the
annotation @EnableFeignClients.
* Next thing is to annotate the CurrencyExchangeProxy with the annotation @FeignClient. Typically, we would name this 
with the application name of the service that we would want to call in here. In this example, it would be named as 
***currency-exchange*** which is present in the application.properties file of CurrencyExchangeMicroservice. The other
thing is to specify the url of the currency-exchange. We need to only specify the domain and not the whole url. The url
to be called should be part of the method definition.

```
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

```
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
service registry, "_**Hey, what are the addresses of the CurrencyExchangeMicroservice?**_" The service registry would
return those back to the CurrencyConversionMicroservice and then the CurrencyConversionMicroservice can send the 
request out to the CurrencyExchangeMicroservice. All the instances would register with the naming server or the
service registry and whenever CurrencyConversionMicroservice wants to find out what are the active instances, 
it asks the naming server, gets to the instances and load balances between them. One of the options that Spring Cloud 
provides is Eureka.

### **Registering CurrencyConversionMicroservice and CurrencyExchangeMicroservice with the naming server**
In order to register any Microservice all we need to do is to have the below dependency in the pom.xml file.

```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

This would automatically register any Microservice with the Eureka Naming Server. Default port of Eureka Naming Server 
is 8761. Each time we restart the Microservice, it would be unregistered and then it would be registered again.

### **Load Balancing with Eureka, Feign and Spring Cloud LoadBalancer**
All that we need to do to load balance between multiple instances of CurrencyExchangeMicroservice is actually remove
the url attribute from the annotation @FeignClient in our CurrencyExchangeProxy interface.

```
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
multiple instances which are returned by Eureka. In the earlier versions of Spring Cloud, the load load balancer which 
was used was ***Ribbon*** and in the recent versions, Spring Cloud shifted to using Spring Cloud LoadBalancer as the 
load balancer. The great thing is, if you're using Eureka and Feign, then load balancing comes for free.

### **Setting up Spring Cloud API Gateway**
We implemented CurrencyConversionMicroservice, CurrencyExchangeMicroservice, LimitsMicroservice and in typical 
Microservices architecture, there would be hundreds of Microservices like these and these Microservices have a lot of
common features - authentication, authorization, logging, rate limiting. Where do you implement all these common
features? That's one of the typical questions in a Microservices architecture and the typical solution is to go for
an **API Gateway**.

In the older versions of Spring Cloud, the popular API gateway to use was Zuul. Since Zuul 1 is no longer supported by
Netflix, Spring Cloud has moved on and now the recommended option as an API gateway is **Spring Cloud API Gateway**.
