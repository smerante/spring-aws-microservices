# Deploy Spring Boot Applications to AWS using Elastic Beanstalk
[Udemy Repo](https://github.com/in28minutes/deploy-spring-boot-aws-eb)


# JDBC 

Java DB conectivity, calls db with querries and needs to manually map between beans and tables

# Spring JDBC 
Spring abstracts a layer of JDBC which allows for easier mapping between tables and quieries including updating beans. 

# JPA
Java Persistence API ( JPA = interface, Hibernate=imlpementation ) allows mapping classes to table, and specifying relationships and unique IDs

Dependencies
Web, JPA (DB Support Layer), H2(In memory DB)

At application launch spring sees that H2 is a dep and enabled and autowires/configures the entities with JPA

JPA Will save and create the db in local memory based on the entities defined 

Spring autoconfigures the entities to h2 tables and uses jpa to insert entries into the table


In your EB apps you can navigate to environments and terminate them to release all their resources. 

Up until 6 weeks you can restore terminated environemnts 


03 Webapp deploying WAR to aws

In application.properties specifiy the webapp props: 
spring.mvc.view.prefix: /WEB-INF/jsp/
spring.mvc.view.suffix: .jsp
logging.level.: DEBUG

Include dependencies for tomcat-embed-jasper: 
<dependency>
 <groupId>org.apache.tomcat.embed</groupId>
  <artifactId>tomcat-embed-jasper</artifactId>
  <scope>provided</scope>
</dependency>

To allow h2 console in app.props, add the following line:

spring.h2.console.settings.web-allow-others=true

Proxy logs when creating a JAR => NGINX
Proxy logs when creating a WAR => APACHE HTTPD


[Running Docker](https://github.com/in28minutes/docker()
Running docker container: 
docker run -p local_port:docker_server_port hub.docker.package:version
docker run -p 8080:5000 in28min/todo-rest-api-h2:1.0.0.RELEASE

run in detached mode add -d
docker run -d -p local_port:docker_server_port hub.docker.package:version

Check containers after running them: 

docker container ls -a / /List all container 
docker container run ##id## // run a container with certain ID
docker container rm ##id## // remove containers
docker images
docker image history ##id## //See all the cmds to create the img


docker client -> docker daemon -> pull from hub.docker -> create local image -> create container

Manually build a jar
docker run -dit openjdk:8-jdk-alpine //-dit detacthed interactive, launches interactable command shell
docker container cp target/docker-in-5-steps-todo-rest-api-h2-1.0.0.RELEASE.jar ##ID##:/tmp
docker container exec romantic_aryabhata ls /tmp
docker container commit romantic_aryabhata in28min/manual-todo-rest-api:v1
docker container commit --change='CMD ["java","-jar","/tmp/docker-in-5-steps-todo-rest-api-h2-1.0.0.RELEASE.jar"]' romantic_aryabhata in28min/manual-todo-rest-api:v2
docker run -d -p 5000:5000 in28min/manual-todo-rest-api:v2

To automate this process

1. pom.xml include a mvn pluggin for docker 
<plugin>
  <groupId>com.spotify</groupId>
  <artifactId>dockerfile-maven-plugin</artifactId>
  <version>1.4.10</version>
  <executions>
    <execution>
      <id>default</id>
      <goals>
        <goal>build</goal>
        <!-- <goal>push</goal> --> 
      </goals>
    </execution>
  </executions>
  <configuration>
    <repository>in28min/${project.artifactId}</repository>
    <tag>${project.version}</tag>
    <skipDockerInfo>true</skipDockerInfo>
  </configuration>
</plugin>

2. Dockerfile
FROM openjdk:8-jdk-alpine
VOLUME /tmp
EXPOSE 5000
ADD target/*.jar app.jar
ENTRYPOINT [ "sh", "-c", "java -jar /app.jar" ]

docker run -d -e MYSQL_ROOT_PASSWORD=1234 -e MYSQL_DATABASE=todos -e MYSQL_USER=todos-user -e MYSQL_PASSWORD=123 mysql:5.7


msqlsh //starts mysql shell
\connect todos-user@localhost:3306 //connect to db using port and user name
\use todos //use todos table as schema 
\sql //start sql commands

Connnecting to RDS db through elastic beanstalk happens automatically through env variables

To allow inbound/outbound traffic configure security groups on the RDS instance from configuration
add rule to allow traffic from your IP to test

size of db: 

SELECT table_schema "todos",
        ROUND(SUM(data_length + index_length) / 1024 / 1024, 1) "DB Size in MB" 
FROM information_schema.tables 
GROUP BY table_schema; 


# Connecting through external DB from EB -> RDS
Create environment property tags for each key ('RDS_HOSTNAME','RDS_DB_NAME') and set values from DB instance from RDS
Once you hit apply the env will auto update and restart the war

Update security groups to allow inbound traffic from EB config ec2 security group

To avoid errors when terminating eb env you need to create a new security group in EC2 and add the security group id to it from RDS. 

Go to EB configuration and add the security group to your instance

Go back to EB, and restart to app to make sure it still connects to DB


# Full stack react backend
To understand JWT and auth more: https://www.springboottutorial.com/spring-boot-react-full-stack-with-spring-security-basic-and-jwt-authentication

When deploying app to S# make sure the region is the same from the EB bucket

You can add a public bucket policy:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AddPerm",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::full-stack-frontend-qa/*"
        }
    ]
}