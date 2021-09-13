## Docker & Microservices - A match made in heaven
As part of our microservices architectures, we build a number of small focused microservices. One of the biggest 
advantages of microservices is the flexibility to innovate and build applications in different programming languages. 
You can build microservices using Java and Spring Boot, or you can also build microservices using Go, Python, 
JavaScript, and a variety of other programming languages. But, as we start exploring the microservices architecture 
and start implementing different languages, the deployments of these microservices become complex.

#### MovieService -> DB1     CustomerService -> DB2     ReviewService -> DB3     BookingService -> DB4     FareCalculationService -> DB5

Let's say, the Movie microservice and the Customer microservice are implemented in Java and let's say the other ones
are implemented in Python. You do not want different deployment procedures for each of these microservice types. How 
can you get a common way to deploy multiple microservices irrespective of the language or the framework that is used 
to build these microservices? How can we get one way of deploying Go, Java, Python, or JavaScript microservices? That's 
where **Containers** come into picture and the most popular container tool is **Docker**. What you can do is to create 
Docker images for each of these microservices. 

So, for all the microservices that we looked above, you can create **Docker images**. The Docker image contains everything 
that a microservice needs to run: 

* Application Runtime
* JDK
* Python or NodeJS
* Application Code

For Java applications, it might just be the JAR, your dependencies that you need to run the application. So, everything is
part of your Docker image and once you have this Docker image, you can run these as Docker containers, the same way on any
infrastructure. So, you can run this Docker images on your local machine, in a corporate data center, and also in 
the Cloud; whether it is AWS, Azure or Google Cloud, all of these support running Docker containers in a wide variety
of ways. The containers are one of the most important evolutions as far as microservices architecture is concerned in 
the last decade.

## First Docker Usecase - Deploy a Spring Boot Application
Let's discuss a usecase where Docker is most useful. You're working on a team and you are the star developer in the team.
You're working on a challenging project with very tight deadlines. You'd want a application deployed to an environment quickly.
You have a very good friend of yours working in the operations team and you go to him and tell, 

> "OK, let's quickly deploy the application to the QA environment. Launch up either terminal or a command prompt and type this 
> along and let's check if Docker is installed.

```docker --version```

> "Docker is installed. Now, let's run the application."

```docker run in28min/todo-rest-api-h2:1.0.0.RELEASE```

You press Enter and you see that lot of magic unfolds. Your friend from the operations team asks what's happening. You tell him 
> Hold on, let's see what would happen.

You'd see that right now it's downloading/pulling something. You would now see that a Spring Boot application is being launched 
up and you'd see that in about a 20 seconds, you are able to launch up a Spring Boot application. Your operations team friend is 
stunned. He tells you,

> Hey, on this machine, Java is not installed, then how is it running?

Your friend in the operations team is actually working in a few older projects. He's used to the manual approach. He usually gets 
a document which says, 

> You need so and so hardware and you'd want to install so and so version of Linux OS and on top of it, install JDK 1.8, install 
Tomcat version 8.x.x and then download the JAR and then run it using this command. 

What he tries to do typically is, try to follow these instructions and install the application and typically he makes a lot of
mistakes. When he is looking at a document and trying to type it down a multiple set of instructions, there's a high chance that 
he make a mistake while deploying something. He's asking you now, 

> How are we able to deploy this application that you've built so easily?

You tell your friend,

> OK, that's the magic of Docker!

## **Docker Concepts - Registry, Repository, Tag, Image and Containers**
Where is the project getting downloaded from in the previous step? Let's answer some of the important questions in this specific step. In the previous step, what is happening is an image is being downloaded from something called as **Docker Hub (https://hub.docker.com)**. Docker Hub is also called as a **Docker Registry**.

A registry contains a lot of repositories, a lot of different versions of different applications, and because this is a public
registry, anybody can access this. Typically when you're working in an enterprise, we would be using private repositories, so that our
images can only be accessed by somebody who has the right credentials. If this is a registry where our application is, how do we
locate our application?

To locate our application in the Docker Hub, we type - **https://hub.docker.com/r/in28min/todo-rest-api-h2**. If you type this in and 
press Enter, you would go to something called a **Repository**. So, **https://hub.docker.com** is a registry, it can contain multiple 
repositories, and inside that, **in28min/todo-rest-api-h2** is a repository storing all the versions of a specific application. So, 
this is something that hosts a number of tags and we specified **1.0.0.RELEASE** as the version that we would want to use. Now you 
might be wondering, what does this image contain? You'd see that the image is about 102 MB. This image actually contains all the 
things that your application needs to run. It contains the right software. For example, a specific version of Java; Java 8 or Java 11, 
whichever version you'd want. It contains all the libraries your API needs. Your todo-rest-api might need 15 libraries; it contains 
all of them and it contains any other dependency that your application might need to be able to run. 

When we ran the command, ```docker run in28min/todo-rest-api-h2:1.0.0.RELEASE```, this image was downloaded to our machine. So, a 
local image was created. So, from the registry, the image is downloaded to your machine and once the image is downloaded, it is ran as
an application in your machine and this is what is called a **Container**.

**Image is something static**. So, on the repository, image is a set of bytes, that's it. When it's downloaded, even then the image is 
just a set of bytes and when it's running, it's called a Container. So, image is a static version and **Container is a running version of the image** and for the same image, you can have multiple containers running.

To access the application, we need to run the command with an extra option as shown below

```docker run -p 5000:5000 in28min/todo-rest-api-h2:1.0.0.RELEASE```

**NOTE: -p {HostPort}:{ContainerPort}**

What happens is by default, any container that you run is part of something called a **bridge network** in Docker. You can kind of 
think of it like an internal Docker network. Nobody will be able to access it, unless you specifically expose it onto the host, onto 
the system, where your container is running. What we are doing in here is we are saying ***I'd want to take the internal port, that is, the container port 5000, and map/publish it to a host port through a port on the system where the container is running, which is 5000.*** You'd see that the application is launched up. Next, you would be able to access the APIs that are exposed as part of the application.

## **Playing with Docker Images and Containers**
One thing to know is you'd want your application to be always running. You don't want it to be killed when you do Control-C. How can 
we do that? We can do so by using the **-d option**.

```docker run -p 5000:5000 -d in28min/todo-rest-api-h2:1.0.0.RELEASE```

Here, -d stands for **detached mode**. This would run the container in the background. We don't want to tie up the terminal to the 
lifecycle of the container. So, we'd want to detach it from that. If you press Enter, you would get a container ID and that's it. If 
you'd want to see the logs, you can say ```docker logs <container_id>```. By this, you can see the some of the logs from that specific 
application. If you'd want to keep following the logs, the way you can do that is by using **-f option**.

```docker logs -f <container_id>```

This would start tailing the logs. It would start following the logs of that specific application. So, if there is any new log coming 
in, I would be able to see it.

We can run multiple containers from the same image. What we can do is, we can run another version of the same application on port 5001. ```docker run -p 5001:5000 -d in28min/todo-rest-api-h2:1.0.0.RELEASE```

To view all the images which are present in your machine, we can use ```docker images```. This command only shows the images that are 
local to us, the images which have been pulled from the Docker Registry. It's not completely showing all the images which are present inside the Docker Registry / Docker Hub. If you'd want to see any image down here, the first thing you would need to do is to pull the image down and once you pull the image down, you'd see that when you type in this command.

The command to view all the containers that are running. We can use ```docker container ls -a```. This shows all the containers 
irrespective of their status. That is whether a container is running or in exited state.

The command to stop the containers which are running, we use ```docker container stop <container_id>```.

## **Understanding Docker Architecture - Docker Client, Docker Engine**
The place we were running the commands in is called a **Docker Client** and when we type something in the Docker client, the command 
is sent out to something called a **Docker Daemon** or a **Docker Engine** for execution. So, even the local installation of Docker 
uses a client-server kind of architecture. So, when we install Docker Desktop, we were installing both the Docker Client and the 
Docker Daemon. The Docker Daemon is responsible for managing the containers, it's responsible for managing the local images, and it is 
responsible for pulling something from the Image Registry if you'd need it or pushing a locally created image to a Image Registry. 
The first two parts of that is very easy. The Docker Daemon is responsible for Containers and Local Images.

When we ran the command ```docker container ls -a```, you would see all the images that we have created and stopped. These containers 
were managed by the Docker Daemon or the Docker Engine which is running on our local machine. The Docker Daemon also manages the 
images which were downloaded. The Docker Daemon knows that there is a local image for a specific repository and a specific tag. Now, 
if I would want to run a container based on the same image, then it doesn't really download the image again. It knows what containers 
and images are present in local and it knows that this specific thing is already available and it runs an existing thing. If in case, 
you use a different tag, say, 0.0.1-SNAPSHOT.

```docker run -p 5000:5000 in28min/todo-rest-api-h2:0.0.1.SNAPSHOT```

Here, the Docker Client sends it to the Docker Daemon and the Docker Daemon now sees that the image is not available locally. It says,
> In this repository, with this specific tag, the image is not available locally.

So, it goes to the Docker Registry and fetches that image down for us and then runs it as a container. The Docker Daemon is
responsible for managing our local stuff and also pulling something from the image repository, if something is not available on our 
local. One of the additional capabilities that Docker Daemon has is, it can process instructions to create images as well. Docker 
Daemon would help us in creating those images and also pushing the images to the Image Registry so that somebody else can make use of 
them.

## **Why is Docker so popular?**
What's happening these days is most of our environments are deployed on the Cloud. The awesome thing about Docker is you can install 
Docker on Cloud also very, very easily. Most of the Cloud providers actually provide container-based services. So, they provide services where you just need to tell, run this container and it would automatically run on the Cloud.

Before Docker, Virtual Machines were very popular. You have the hardware, you have a host operating system installed on top of the 
hardware, and we have something called the **Hypervisor** to manage your virtual machines. So, each of these was a virtual machine.
One of the major problems with these virtual machine architecture was typically these are heavy weight. We had two operating systems; Host Operating System and Guest Operating System and that makes the whole thing a little heavy and that's where Docker comes in.

If you have some infrastructure and if you have some Host Operating System installed on top of it, all that you need to do is install 
the Docker Engine for that specific operating system and Docker would take care of managing these containers. The Docker image 
contains all that is needed to run a container. All the libraries, all the software are directly part of these containers. Because 
there is just one OS, the Host OS, Docker is relatively lightweight and therefore it is very, very efficient. That's why you'd see 
that all the Cloud providers provide a number of services around Docker. 

Today, as we speak, it's very, very easy to deploy something related to Docker onto any of the Cloud providers. **Azure** provides a 
service called **Azure Container Service**. **AWS** provides a service called **Elastic Container Service**. So, the thing 
which we are understanding right now is the fact that using Docker on local is very easy and using Docker on the Cloud is also very easy and that's the reason why Docker is becoming really, really popular during the last few years.

## **Playing with Docker Images**
We have already used ```docker images``` which will list all the images present locally. However, you can give two, three or multiple
tags to a single image. To do that, we should use - ```docker tag <repository_name>:<existing_tag_name>  <repository_name>:<new_tag_name>```. Now, the image would be shown twice with same image id but with two different tags. Typically, a 
lot of projects, in addition to the `RELEASE` version, will use a specific tag called `latest` on the most recently released version 
of that specific project. However, it's very, very important to remember the fact that latest does not need to always point in to the 
latest RELEASE; it's just another tag, like 1.0.0.RELEASE, not an automated thing but a thing that you'd need to manually add. So, if 
latest is actually pointing to this, then latest would actually download this version of your repository.

The interesting thing with Docker is, if you do a ```docker pull mysql``` without specifying a version then it will pull down the 
```latest``` version from the Docker Registry. However, you have to remember the fact that the latest tag might not really be the 
latest. So, a rule of thumb is to go to repository, check the tags, and check which release the latest tag is attached with, and then 
make sure that you are really using the latest release. 

Now, one of the important things that you'd need to know about the `pull` command is, it would just download the image from the 
registry to your local machine. If you'd want to run it, you need to use ```docker run```. Docker run checks if it's available in the 
local. If it's not available in the local, then it pulls it and creates a container.

The other thing we talked about is the fact that mysql is an official image. What is an official image? If you do ```docker search mysql```, it would search for any image which contains mysql. Once done you would see that the first one which is listed is mysql and 
it says, OFFICIAL [OK]. This is something which is Docker Official. Basically, **Docker Official Images** are a curated set of Docker 
repositories. Docker has a team which looks at these images, make sure that they are meeting certain standards, and they publish all 
these content in the official images. So, whenever you would want to use a image for some software, let's say, you'd want to use a 
MySQL image or let's say, you'd want to use a Tomcat image or you you'd want to use a Java image, make sure that you are using an 
official Image.

You can do ```docker image history <image_id>/<repository_name:tag>``` and look at the history of a image. What you would see are the 
steps involved in creating that specific image. You'd see that in certain steps, there is a file size attached. So, there is a layer 
added in with about 5 MB and after that, there are a number of instructions which were run to create that image, and after that, there 
is another layer added in with 99.3MB which is the largest layer in here and this one is typically the layer where we add in Java. So, 
the JDK which is added in is about 99 MB and after that, there are couple more instructions which are run. You would see that we are 
exposing port 5000 on that specific image and you can see that a file is added in. Typically, when we are talking about Spring Boot 
application, that's a jar file. So, you would see that, the size is about 38 MB and there were a few Java options which were set and a 
command is set to run something when a container is created from this. 

The important thing for you to understand is, when you do a ```docker image history <image ID/repository_name:tag>```, you'd get all 
the history, all the steps that were involved in creating that specific image. 

You can also do something called ```docker image inspect <image_id>```. When you do the inspect, 

* you can see the `tags`
* you can see the `digest`
* you can see when it was `created` 
* you can see the `containers` which were created from it
* you can see some `configuration` related to the image
* you can see the `entrypoint`, which is basically the command which would be run when you create a container out of this image and launch it
* you can see the `DockerVersion`, which was used to create it
* you can see the `ExposedPorts`, you can see that this is actually running on some port 5000
* you can see all the `environment variables` which were set; PATH, JAVA_HOME, JAVA_VERSION, JAVA_ALPINE_VERSION and all that stuff
* you can see a lot of `information about the folder structure`, the different layers which are present and all that stuff.

You can also remove an image from your local machine using ```docker image remove <image_id>``` and not really delete it from the Docker Registry.

## **Playing with Docker Containers**
Earlier we created a container by using ```docker run -p 5000:5000 -d in28min/todo-rest-api-h2:1.0.0.RELEASE```. The interesting thing is the fact that this is actually a shortcut for a command, actually called 
```docker container run -p 5000:5000 -d in28min/todo-rest-api-h2:1.0.0.RELEASE``` but the thing you'd need to understand is actually 
what you are running is a container. So, we are creating a container from a specific image. This would actually launch up a docker container. 

One of the interesting things is, you can pause and unpause a container. So, you can say, 
```docker container pause <ID_from_detached_state>```. Once you do this, the container would be paused. If you check the logs of this 
container, you'd see that the application is started up, but when I try to access the URL, you would not get a response back because 
this is in a paused state. To unpause it, ```docker container unpause <ID_from_detached_state>``` after which you would get a response
back from the application URLs. So, pause just stops the container in that specific state.

```docker container stop <container_id>``` would stop a running container. You can also inspect a container by using 
```docker container inspect <container_id>```. When you do a docker container inspect, you can see tons of details about that specific 
container, the image it is created from, a lot of a metadata about the container itself when it was created, the current status, You 
can see what's the platform, the details about the image, the port bindings, the volumes and a lot of other stuffs. Whenever we are 
expecting something to happen with a container and it's not happening, probably this is the place you can look at. This would give you 
tons of data, which you can look at and see what's going wrong. 

The other interesting command related to the containers is ```docker container prune```. This would remove all the stopped containers 
from the local machine.

Earlier, we looked at the ```docker container stop``` which would gracefully shutdown the container. What do I mean by a graceful 
shutdown? When you shutdown a container, in the logs at the end you can see, after the starting of the application, there is something 
called Shutting down ExecutorService. its closing the EntityManagerFactory and then, it's dropping the table sequences and it's 
shutting down the connection pool. So in a graceful shutdown, the container was given some time to finish its processes to shutdown 
gracefully. So, things like closing out the connection pool, things like shutting down the ExecuterService, the container was given 
time to do all that stuff. In technical terms, when I do a container stop, the signal which is sent to the container is something 
called ```SIGTERM```. It means, take about 10 seconds and make sure that you gracefully complete your execution.

Another command is ```docker container kill <container-id>```. By doing this, the container would be stopped as it is. It was not 
really given time to do anything. It stopped dead. In technical terms, what happens is, in here, a signal called ```SIGKILL``` is sent 
out to the container and it immediately stops. The command which you should use most of the times, actually 99.99% of the times is 
```docker container stop```. You would want to give your container time to shut down gracefully.

Let's start up our earlier application in detached mode with restart policy of always as shown.
```docker run -p 5000:5000 -d --restart=always in28min/todo-rest-api-h2:0.0.1.SNAPSHOT```. Here, --restart=always, this is called a 
restart policy. Two of the most popular values for a restart policy are `always` and `no`. The default is `no`. If you specify 
`always` and start the application up, what the policy would do is whenever we restart our Docker Desktop, our container would be 
automatically started. Whenever you restart the Docker Daemon restarts, it sees if there are any container with the restart policy of 
always and would launch them up. The only way you can actually prevent the restart from happening is to first stop this up.

## **Playing with Docker Commands - stats, system**
The first command we will be looking at is ```docker events```. This would help us to see what events are happening with Docker 
Daemon. Events like containers being stopped, being disconnected from the network, volume unmounted, etc. So, if you'd want to see 
what's happening with your Docker environment, what is it doing, you can use this command to see what's happening in the background.

The other interesting command is ```docker top <container-id>```. This is actually used to see all the processes which are running in 
that specific container.

The other interesting command is ```docker stats```. This would show all the stats regarding the containers which are running, what 
its name is, how much amount of CPU is it making use of, how much memory is it making use of, what is the limit of memory, what is the 
memory percentage. You would be able to see all the metrics, stats around your running containers.

We can run a container with a specific memory and CPU limits as shown.
```docker run -p 5000:5000 -m 512m --cpu-quota 5000 -d in28min/todo-rest-api-h2:0.0.1.SNAPSHOT```

We can use the `-m` option to specify the maximum memory the specific container to run on. You can also assign a CPU Quota. Typically, 
the entire quota which is present is 100000. So, out of a 100000, which is 100%, we can allocate a CPU quota to a specific container. 
Here in this example, we are assigning 5% (5000) of the CPU to this specific container. The same can be verified by running 
```docker stats```.

The next command would be ```docker system df```. It helps us to look at all the different things that our Docker Daemon manages. So, 
Docker Daemon manages images, containers, something called volumes.

## **Introduction to Distributed Tracing**
From here on, let's try and run microservices using Docker. Let's create containers for most of these microservices, and try and run 
them using Docker. The first feature that we would be exploring with Docker is **Distributed Tracing**. 

Microservices typically have complex call chains. How do you debug problems? How do you trace requests across microservices when a 
request goes across from one microservice to another? How do you find out where the problem is? How do you trace the request across 
the multiple microservices and identify the specific microservice where the problem is? That's where we go for a feature called 
```Distributed Tracing```.

#### **How does Distributed Tracing work?**
What would happen is, all the microservices involved would actually send all the information out to a single Distributed Tracing 
Server and this Distributed Tracing Server would store everything to a database. This database can be a in-memory database or a real 
database. The Distributed Tracing Server would provide us with an interface which would allow us to trace the requests across multiple 
microservices.

Here, we will launch a Distributed Tracing Server called `Zipkin` as a Docker container and connect all our microservices to this 
specific Zipkin server.

## **Launching Zipkin Container using Docker**
The command to launch up the distributed tracing server is, ```docker run -p 9411:9411 openzipkin/zipkin:2.23```. Here, 
`openzipkin/zipkin` is the authoritative image for Zipkin, a distributed tracing system. Instead of creating a Java project for 
Zipkin, what we are doing is, we are launching it as a Docker container and this is very, very cool because you don't really need to 
store anything about Zipkin in your local machine. All that you need to run is a Docker command. It would download the Docker image 
for openzipkin and it would automatically run it. We don't really need to worry about anything related to Zipkin.

Once the Zipkin disributed tracing server start up, you can launch it up via `http://localhost:9411/zipkin/`. Next we will try to 
connect all the microservices to Zipkin.

## **Connecting CurrencyExchange microservice to Zipkin**
Let's try and trace everything that comes to CurrencyExchangeService using Zipkin. The first thing I would need to do is I would need to add in a set of dependencies.

Whenever we are talking about microservices and tracing, you'd want to be able to trace the request across multiple microservices. And 
to be able to do that, each request should be assigned a unique ID. So, spring cloud starter sleuth is one of the frameworks which 
actually assigns a unique ID to each request. As the request goes across multiple microservices, the ID is maintained and the 
information is sent out to the tracing server using that specific ID and this allows us to trace the requests across multiple 
microservices. That's the reason why we would need spring-cloud-starter-sleuth.

```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```

The next dependency that we would want is spring-cloud-sleuth-zipkin. This is a Zipkin specific dependency that we are adding in.

```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

The next dependency that we would add in is not really needed for now is,

```
<dependency>
  <groupId>org.springframework.amqp</groupId>
  <artifactId>spring-rabbit</artifactId>
</dependency>
```

Right now, what we are trying to do is actually directly connect to distributed tracing server over HTTP. So, API Gateway, 
CurrencyConversionService, CurrencyExchangeService, all of these would be sending the information out to the Distributed Tracing 
Server using HTTP calls. And the distributed tracing server would be storing it to an in-memory database and be providing a UI around 
it.

However, consider a scenario when the Distributed Tracing Server is down. What would happen? he information from the microservices is 
lost and to prevent that from happening what we can do is, we can have a queue in between. We can have a queue like RabbitMQ in 
between. All the microservices can send the information out to the RabbitMQ queue and the Distributor Tracing Server can pick up the 
information from the RabbitMQ. This would ensure that even if the Distributor Tracing Server is down, the microservices can keep 
sending messages to RabbitMQ. And when the Distributor Tracing Server is up, it can pick up the messages from the queue which is the 
recommended architecture.

However, in this specific step, we'd be implementing this specific architecture where we would be using HTTP. We will be running the 
above architecture a little later when we will create individual containers for these microservices and will be running everything in 
this picture as a container. 

In addition to the three dependencies, we would need a simple configuration, it's called `Sampling configuration`. Whenever we talk 
about sleuth or distributed tracing, we don't want to trace every request that goes between the microservices. What we want to do is 
to sample a percentage of the requests. If it trace every request, then there'll be a big performance impact. To avoid that, we 
configure something called `sampling`. We would want to sample, let's say, 10% or 20% of the requests. The way we can configure 
sampling is by going to `application.properties` for CurrencyExchangeService.

This below property would trace only 10% of the requests coming in for CurrencyExchangeService.

```
spring.sleuth.sampler.probability=0.1
```

Once done, we can launch CurrencyExchangeService using `http://localhost:8000/currency-exchange/from/USD/to/INR`. However, the new 
thing is that we have added in tracing. If you go ahead and refresh in Zipkin Distributed Tracing UI, you'd see now that there is a 
trace. There will be a trace ID which is assigned to this and you can find all the details related to the endpoint to which the 
request was made.

However, this is not useful. There is just one particular microservice which is connected to distributed tracing server. However, only 
when we connect all the other microservices, you will see the magic unfold. Spring Cloud Sleuth will assign unique IDs to each and 
every request that we make to an endpoint. This is really, really useful to trace the requests across multiple microservices.

## **Connecting CurrencyConversionService and API Gateway with Zipkin**
We need to copy the same dependencies inside CurrencyConversionService and API Gateway microservices that we added to 
CurrencyExchangeService microservice.

```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>

<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>

<dependency>
  <groupId>org.springframework.amqp</groupId>
  <artifactId>spring-rabbit</artifactId>
</dependency>
```

The next thing to configure is the sampling the same way we did for CurrencyExchangeService microservice.

```
spring.sleuth.sampler.probability=1.0
```

So, API Gateway, CurrencyConversionService, and the CurrencyExchangeService all have the configuration that they would need to connect 
to Zipkin Distributed Tracing Server.

You can also see that we are launching up all the microservices manually and this is very, very tedious job to check manually if 
each and every microservices whether they are up and then firing requests against them takes a lot of time. We would be looking at 
something called as `Docker Compose`, which would really help us to make the launch of these different microservices very, very easy.

Once the setup is completed, you can see that there are a lot of requests in here. You would be able to see all the URLs that you
fire; CurrencyConversionService, API Gateway, CurrencyExchangeService. We usually pick up one of the API Gateway ones with a span 
of 5. This means it's one which is going through multiple microservices. Any request fired would be intercepted by the API Gateway 
will be redirected to the CurrencyConversionService and in CurrencyConversionService it would in turn call the CurrencyExchangeService.

### **How are the microservices able to locate the Zipkin distributed tracing server?**
We just added a few dependencies and somehow magically, they were able to find the distributed tracing server and send the logs over. 
How is that happening? That's happening because of the default configuration. By default, `spring.zipkin.baseUrl` a property you can 
configure with the Zipkin URL, is configured with `http://localhost:9411` and because we are actually running Zipkin on the same URL, 
it's able to easily find it. If you are running Zipkin on a different URL, you might also need to configure this as well.

## **Getting setup with microservies for creating container images**
Now, let's run each of these microservices as containers and see how we can actually launch them up in a very, very easy way. Instead of manually launching up each application or container individually, is there a way we can actually launch up all of them together?