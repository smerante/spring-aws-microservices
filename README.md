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
