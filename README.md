# Welcome to Spring Lab!
Welcome to the technical documentation for Spring Laboratory, a project designed to assist with the development and testing of applications using the Spring framework. This documentation consists of three main parts:
1. The objective of the project: In this section, we will explain the purpose and goals of Spring Laboratory, as well as how it can benefit developers working with Spring.
2. The Spring architecture: In this section, we will provide an overview of the Spring framework and its various components, including how they work together to support the development of applications. 
3. How to use the laboratory: In this section, we will provide detailed instructions on how to set up and use Spring Laboratory, including any necessary configurations and dependencies. So, you can easily get started with your Spring projects.

# Project objective
The objective of Spring Laboratory is to provide developers with a convenient and efficient environment for testing both individual microservices and the communication between multiple microservices.

One of the key features of Spring Laboratory is its agility and portability. It can be easily initialized with a single command thanks to Docker, making it a versatile tool that can be used in a variety of development scenarios. Whether you are working on a single microservice or testing the interactions between multiple microservices, Spring Laboratory has you covered. It is a valuable resource for any developer working with the Spring framework.

# Spring architecture
![Spring Architecture Diagram](https://i.ibb.co/KN6YWR2/Spring-Architecture.jpg)

Spring's architecture is composed by few basic elements with one or more microservices. As we can see in image above, we have the following components:
- Gateway: Component in charge of validate access token through an authorization server and route request made from the client to appropriate service.
- Discovery server: Component in charge of keeping services registries and provide the appropriate endpoint to gateway when needed. It is also used to check the services' status.
- Configuration server: Component in charge of providing the appropriate configuration to each service or component. This component extracts configuration files from a Github repository where we can find one .Properties file for each service and environment. For example, we can find two .Properties files for a service called demo, one file for the development environment and another file for the production environment.
- Authorization server: Component in charge of providing security to our system. This component manages everything related to access tokens provided by clients.
