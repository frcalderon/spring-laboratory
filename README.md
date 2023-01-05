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

Spring's architecture is composed of a few basic elements with one or more services. As we can see in the image above, we have the following components:
- Gateway: Component in charge of validate access token through an authorization server and route request made from the client to appropriate service.
- Discovery server: Component in charge of keeping services registries and provides the appropriate endpoint to gateway when needed. It is also used to check the services' status.
- Configuration server: Component in charge of providing the appropriate configuration to each service or component. This component extracts configuration files from a Github repository where we can find one .Properties file for each service and environment. For example, we can find two .Properties files for a service called demo, one file for the development environment and another file for the production environment.
- Authorization server: Component in charge of providing security to our system. This component manages everything related to access tokens provided by clients.

# How to use
1. Create a Git repository for storing .properties files following the tutorial you will find later in this document.

2. Add the following dependencies to your project:
- In order to use the configuration server, you need Spring Cloud Starter Config dependency.
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```
- In order to use the discovery server, you need Spring Cloud Starter Netflix Eureka Client dependency.
```
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```
- In order to use the authentication server, you need Spring Boot Starter Security, Spring Security Oauth2 Resource Server and Spring Security Oauth2 Jose dependencies.
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-oauth2-resource-server</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-oauth2-jose</artifactId>
</dependency>
```

3. If you want to use the authorization server, apart from dependencies in the point 2, you will need to create a configuration class. I recommend you create a class called SecurityConfiguration in the config package of your project. The content of this class is the following:
```
@Configuration
@EnableWebSecurity
public class SecurityConfiguration {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
                .oauth2ResourceServer(OAuth2ResourceServerConfigurer::jwt);

        return http.build();
    }
}
```

4. Add the following properties to your **application.properties** file.
- For using the configuration server:
```
spring.config.import=optional:configserver:http://host.docker.internal:8888
```
- For using the discovery server:
```
eureka.client.service-url.defaultZone=http://host.docker.internal:8761/eureka/
```
- For using the authorization server:
```
spring.security.oauth2.resourceserver.jwt.issuer-uri=http://host.docker.internal:8081/realms/demo
```

5. Add a Dockerfile to your project with the following content:
```
FROM openjdk:11  
RUN mkdir -p /app  
WORKDIR /app  
RUN git clone [Git repository of your project] .  
RUN chmod +x mvnw  
RUN ./mvnw clean install -DskipTests  
EXPOSE 8080  
CMD ["java", "-jar", "target/[project name]-0.0.1-SNAPSHOT.jar"]
```

6. Create a Docker image of your project with **docker build -t [image name]:[version] .** command. Example:
```
docker build -t demo:1.0.0 .
```

7. Clone the following repository: [Spring Laboratory](https://github.com/frcalderon/spring-laboratory)

8. Add your service to **docker-compose.yml** file in the repository. Here you have an example of how to add a service:
```
[service name]:
    image: [image name]:[version]
    container_name: [container name]
    ports:
        - "[host port]:[application port]"
    restart: unless-stopped
    environment:
        - SPRING_PROFILES_ACTIVE=development
    networks:
        demo:
            aliases:
                - "[service alias in demo network]"
    depends_on:
        - [services you want this service to depend on]
```

9. In order to access your service, add the following configuration to the gateway configuration in the configuration repository.
```
spring.cloud.gateway.routes[0].id=your service route id (Example: demo-route)
spring.cloud.gateway.routes[0].uri=your service uri (Example: http://host.docker.internal:[your service port on host machine])
spring.cloud.gateway.routes[0].predicates[0]=Path=your service route path (Example: /demo/**)
spring.cloud.gateway.routes[0].filters[0]=StripPrefix=1 (this filter strips the first prefix of your service path)
```

10. Run the environment with **docker-compose up -d** command.

11. Access your service through **http://host.docker.internal:8080/[your service path]**

## Considerations
- Each service has to be in network **demo** with an alias.
- Each service has to be above port 9000 of the host.

# Configuration repository

In order to use the configuration server, you need to create a configuration repository for storing your services' .properties files. To do that you need to follow the following steps:

1. Create a Git repository, for example, in Github.

2. Add .properties files for your services following the next scheme:
```
[service-name]-[environment].properties

Example: demo-service-development.properties
```

3. Add the properties your service needs to work properly.

4. In order to set your configuration repository as the main repository that configuration server is going to use, you need to edit the **docker-compose.yml** file. Go to the configuration server definition and add the following environment variable:
```
environment:
    - SPRING_CLOUD_CONFIG_SERVER_GIT_URI=[your configuration respoitory url]
```