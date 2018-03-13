Fuse Integration Services (FIS) SOAP to REST Proxy Demo
====================================

Demonstration of a SOAP to REST Proxy for an existing SOAP service, using Fuse Integration Services 2.0.  A video walkthrough demonstrating this project can be found [here](https://youtu.be/TLOLWMeobuU).

## Overview

This project demonstrates a microservices based project leveraging SpringBoot and Apache Camel to proxy an existing SOAP service with a new REST front-end service.  Additionally, the REST API is documented using Swagger.  The project makes use of camel-servlet component listening on port 8090 and configured using Spring Boot.

## Prerequisites

This project requires [gs-soap-service](https://spring.io/guides/gs/producing-web-service/) in place and running a SOAP service at port 8080. 

An OpenShift environment must be present for deployment to a cloud environment. For the purpose of testing, I prefer to use [Minishift](https://fabric8.io/guide/getStarted/minishift.html). To test this in OpenShift, the soap service should be deployed as well and the URLs adjusted accordingly in `.wsdl` file to correct the endpoints to the SOAP service.

## Deployment

This project can be deployed using two methods:

* Standalone Spring-Boot container
* On an Openshift environment using Fuse Integration Services 2.0

## Standalone Spring Boot Container

The standalone method takes advantage of the [Camel Spring Boot Plugin](http://camel.apache.org/spring-boot.html) to build and run the microservice.

Execute the following command from the root project directory:

```
mvn spring-boot:run -Dspring.profiles.active=dev -Dserver.port=8090
```

Once the spring boot service has started, you can test the REST API by executing the following command

```
curl http://localhost:8090/api/countryDetails/Spain
```

A list of German weather stations is returned in JSON format.  Try other countries as needed.

Additionally, you can reach the REST API using the web browser by navigating to http://localhost:8090/api/countryDetails/Spain .  It's also possible to navigate the REST service using the Swagger documentation [here](http://localhost:8090/index.html).

## Openshift / Minishift Deployment

First, create a new OpenShift project called *fis-soap-rest-proxy*

```
oc new-project fis-soap-rest-proxy --description="Fuse Integration Services SOAP to REST Proxy Demo" --display-name="SOAP REST Proxy"
```

Execute the following command which will execute the *ocp* profile that executes the `clean fabric8:deploy` maven goal:

```
mvn -P ocp
```

The fabric8 maven plugin will perform the following actions:

* Compiles and packages the Java artifact
* Creates the OpenShift API objects
* Starts a Source to Image (S2I) binary build using the previously packaged artifact
* Deploys the application using binary streams

## Swagger UI

A [Swagger User Interface](http://swagger.io/swagger-ui/) is available within the rest-soap-transformation application to view and invoke the available services. 

Select the hyperlink for the gateway application to launch the Swagger UI

![](images/swagger.png "Swagger User Interface")

The raw swagger definition can also be found at the context path `api/api-doc` 

## Command Line Testing

Using a command line, execute the following to query the definition service

```sh
curl -s http://$(oc get routes rest-soap-transformation --template='{{ .spec.host }}')/api/countryDetails/Spain 
```
	
A successful response will output the following

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<getCountryResponse xmlns="http://spring.io/guides/gs-producing-web-service">
    <country>
        <name>Spain</name>
        <population>46704314</population>
        <capital>Madrid</capital>
        <currency>EUR</currency>
    </country>
</getCountryResponse>
```
