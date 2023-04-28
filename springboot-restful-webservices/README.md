# Two container(applications) communicating together
For this project, we will use docker compose to deploy two containers that need to communicate with each other.

The RESTful web service appliation (microservice) will be depending on the mySQL databse to persist information. 

These two containers need to communicate together. Docker compose is useful for setting this up very easily.


Note that with docker compose, you do not need to have docker swarm up. So any VM with docker installed should work.

NOTE: You will run into at least one problem when doing this, and I expect you to figure out what it is and fix it.

There is a docker compose file provided, docker-compose.yml. This is the file you will run.

This file defines the services, network and dependencies between the two services:

## Services
Two services are set up, mysqldb and springboot-restful-webservices

### mysqldb

This is deployed by defaults, and operates as database root

### Springboot restful service

The image is build using the Dockerfile file within the project. Take a look at that Dockerfile, it copies an application file ( in this case a jar file, that which was packaged) from the target folder to the final image 
folder. Then builds an image based on the Dockerfile. This means you need to have this jar file prebuild. Ordinarily, the package step of Jenkins would have build this jar (or war file), but in this project, we will manually build it 
using maven, `clean mvn package -DskipTests`. Note the java and Maven are needed to perform a build for this particular project.

The container depends on (see depends_on)  the mysqldb service and be accessible at port 8080, and use th same network the mysqldb is using

## Network

There is one network, which is what the two will use to communicate. For services to communicate, they do so over a network.

springboot-mysql-net

## Prerequisites
* Must have a VM with docker and docker compose
* You need to package the application war file before running the docker compose file
* * You would need to have java and maven installed- that is a Jenkins type environment. 
* * This means installin Java (v17) and Maven, and set the JAVA_HOME, MAVEN_HOME, refer to the Jenkins lesson
* * To avoid this, there is a pre-build war file in the targer folder, you can ust use that. 

## Steps

* Clone the project https://github.com/sirokode/doca/
* Go to the springboot-restful-webservices/ folder
* Manually package the application by running the following maven package command: `mvn clean package -DskipTests`
* * A war file should be generated in the target/ folder
* * BUT That step will fail if you do not have java and Maven, so just depend on the pre-build war file
* Start the two containers by issuing `docker-compose up -d`

## Test that the application is running
Go to IP:8080/api/users

If the site responds( even with no data), it means everything is workign well.

### Extra 
You could play around with the application by adding data to the database. To do so, do a POST request to the users table. 
The application is a RESTful, meaning it exposed not website but an API way to interact with it. You could do this via Postman ( an aplplication for interacting with REST APIs) or just do curl POST on the command line:

`curl -d "firstName=Aplo&lastName=Namgoi&email=aplo@namgoi.ke" -X POST https://IP:8080/api/users`

The above should create a user, and you can see(get) that user by going  to IP:8080/api/users/1

## Shutdown the containers
Issue the command `docker-compose down`
