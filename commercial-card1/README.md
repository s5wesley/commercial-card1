# Spy App

**Java-based Full-Stack Web Application.** Users can view or create missions for each agents. They can also edit/ delete missions.

## Technologies

- Java
- Spring Boot
- JDBC
- H2 Database
- Thymeleaf
- HTML5
- Maven

## Features

- CRUD operations
- JDBC for database control
- Spring Boot framework
- Mapping HTTP requests to appropriate HTML pages on the controller
- Sharing model attributes between the controller and HTML files using Thymeleaf
- Customizing schema using schema.sql file
- Inserting initial data using data.sql file

## How to Run

1. Clone the repository
2. Open the project in your IDE of choice
3. Run the application

## How to change IP Address with slow jenkins

1. cd /var/lib/jenkins
2. sudo vim jenkins.model.JenkinsLocationConfiguration.xml
3. change ip Address 3.84.128.186:8080 to the current one
4. restart jenkins -sudo service jenkins restart