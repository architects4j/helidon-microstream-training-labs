# NoSQL persistence on Java services 

In this exercise you can validate and practice your knowledge of the following concepts:

* Java microservice implementation with Helidon;
* Dev experience when using NoSQL databases with Java;
* Jakarta NoSQL;
* Implicit and constant usage of CDI;

Getting familiar with the use case and the exercise goals described next is highly recommended, as a detailed
step-by-step guide is not provided.

**Friendly advice**: consider leveraging this opportunity to
upskill by making the best use of your knowledge and research skills to code the solution without replicating the
provided solution.

## Scenario

Acme Store contacted you to ask for advise about NoSQL databases usage in MicroProfile-based services. They need a solution built on top of the RESTFul service delivered on the previous exercise. 
If you used the quickstart, by now you should have a microservice that relies on in-memory persistence with MicroStream. We will now add another option of persistence layer to this service, 
allowing it to persist data on [MongoDB](mongodb.com). 

### Goals

**About the service:**

- You should adapt an existing application, the `acme-store-rest` application. It uses Microprofile 3.3 + Helidon and MicroStream for persistence.
- The service handles `Product`, and provides RESTFul operations for it:
    - List all products:  GET "/products/"
    - Find a product by ID: GET "/products/{productName}"
    - Delete a product: DELETE "/products/{productName}"
    - Update a product: PUT "/products/{productName}"
    - Insert a product: POST "/products/{productName}"
- The APIs are documented with the [Eclipse Microprofile-Open-API](https://github.com/eclipse/microprofile-open-api);
- It's persistence layer relies on MicroStream for performant in-memory persistence capabilities.

**Goals:**


## Implementation

1. Add dependency to pom.xml
1. Add MongoDB configurations to `microprofile-config.properties`:
```properties
# MongoDB Configs
document=document
document.database=restaurant
document.settings.jakarta.nosql.host=localhost:27017
document.provider=org.eclipse.jnosql.communication.mongodb.document.MongoDBDocumentConfiguration
```
1. Configure the `Product` as an Entity.
   1. Add the @jakarta.nosql.mapping.Entity declaration;
   2. Add an empty constructor method;
   3. Annotate the attributes with adequate column configurations. Use @Id and @Column. Remember, the attributes can no longer be final.

2. In the package `org.a4j.product.infra` create a new class `DocumentManagerProducer`:
```java
package org.a4j.product.infra;

//TODO: Set this bean as ApplicationScope
class DocumentManagerProducer {

    //TODO: Use CDI to inject this bean
    //TODO: Obtain the `document` value configured in microprofile-config.properties. Use @ConfigProperty.
    private DocumentCollectionManager manager;
    
    // TODO: Configure as a producer with @Produces
    public DocumentCollectionManager getManager() {
        return manager;
    }

    public void destroy(@Disposes DocumentCollectionManager manager) {
        manager.close();
    }
}
```
1. Add a new `ProductRepository` interface. It should extend the `Repository` interface:
```java
extends Repository<Product, String> 
```
2. In the `ProductResource`, let's change the persistence layer. Replace the existing repository based on `Inventory` with our new one `ProductResource`.
```java
//**Before:** 
private Inventory repository;
//**After:**
@Inject
private ProductRepository repository;
```
3. Delete the following injection method as we're already injecting in the attribute level:
```java
    @Inject
    ProductResource(Inventory repository) {
        this.repository = repository;
    }
```

4. At this point, you will notice the code doesn't compile since the method `findAll` does not exist in the repository. Currently, (v1.0.0-b4) the `jakarta.nosql.mapping.Repository` does not offer 
   a `findAll` out-of-the-box. It does offer `save`, `deleteById`, `findById`, `existsById` and `count`, therefore, the only method you need to att to the `ProductRepository` interface to deliver 
   the existing endpoints is the `getAll` method. In the `ProductRepository`, add the method signature. Example:
```java
List<Product> findAll();
```










Here are the pre-requisites for the deliverable:

- The service should provide CRUD operations for products data on top of MongoDB, a NoSQL database. 
  - 


## Implementing the project

We recommend you to try creating the project from scratch. If you need, you can take a look at the [restaurant example](https://github.com/architects4j/helidon-microstream-training-demos/blob/main/restaurant) demonstrated by the instructor.

Next, you can also find some tips.

## Tips and hints

### Creating the project

1. You can use the [microprofile starter](https://start.microprofile.io/) to create your Helidon application based on Microprofile 3.3. 
You don't need to select any of the "Examples for specifications". 

2. You will need to create the POJO `Product.java` with the described attributes.

3. You don't need database persistence. You can use in-memory repository. To get started faster, you can create a `ProductRepository` interface, and an implementation of it, the `ProductRepositoryMemory`. You can use the [RestaurantRepository](https://github.com/architects4j/helidon-microstream-training-demos/blob/main/restaurant/src/main/java/com/otaviojava/workshop/microstream/helidon/restaurant/RestaurantRepository.java) and [RestaurantRepositoryMemory](https://github.com/architects4j/helidon-microstream-training-demos/blob/main/restaurant/src/main/java/com/otaviojava/workshop/microstream/helidon/restaurant/RestaurantRepositoryMemory.java#L11) as an inspiration.  

4. You will need a `ProductController` to implement your REST APIs.
     1. To best fit this application need, it can be configured with `@RequestScope`.
     2. Remember to set the `javax.ws.rs.@Path` according to the goals of the project;
     3. You can use `@Inject` to use your `ProductRepository` and manipulate the data.
     4. Make sure to avoid any exceptions in case an invalid data is sent to your endpoints.
     5. To differenciate your APIs, you can annotate your methods with: `@DELETE`, `@PUT`,  `@POST` and `@GET` from 
       `javax.ws.rs`.
     6. To create an endpoint like `/products/{productName}`, you can annotate the respective method with `@Path("{id}
       ")` and make sure you add a @pathParam to your method. Example:

        ```Java
        public Product findById(@PathParam("id")String id) {}
        ```

### Field validation

To implement the business rules related to the product attributes, you can use Bean Validation annotations on your `Product.java`. 

1. To do so, you need to add the following dependency in your `pom.xml`

    ```xml
            <dependency>
                <groupId>jakarta.validation</groupId>
                <artifactId>jakarta.validation-api</artifactId>
                <version>2.0.2</version>
                <scope>provided</scope>
            </dependency>
    ```

2. You can use the annotations `@NotBlank` and `@Size` from `javax.validation.constraints` on each attribute to define the rules and error message you want to associate with it.

### JPA

To store the information in a relational database without vendor-lockin, you can use Jakarta JPA in your entities. In this example, we'll use Postgresql; thus, we'll include Postgresql driver.

```xml
        <dependency>
            <groupId>jakarta.platform</groupId>
            <artifactId>jakarta.jakartaee-web-api</artifactId>
            <version>8.0.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.2.24</version>
        </dependency>
```

The data source guarantees the application does not know which database you're using, thus to change to any database, you only need to replace both the maven dependency and this data source inside the `web.xml`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="4.0" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd">
    <data-source>
        <name>java:global/JPAExampleDataSource</name>
        <class-name>org.postgresql.ds.PGSimpleDataSource</class-name>
        <server-name>${server.host}</server-name>
        <port-number>5432</port-number>
        <database-name>${server.database}</database-name>
        <user>${server.user}</user>
        <password>${server.password}</password>
    </data-source>
</web-app>
```

The last step is the persistence unit in the `persistence.xml`.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="2.1" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd">
    <persistence-unit name="JPADatasourceExamplePU" transaction-type="JTA">
        <jta-data-source>java:global/JPAExampleDataSource</jta-data-source>
        <exclude-unlisted-classes>false</exclude-unlisted-classes>
        <properties>
            <property name="javax.persistence.schema-generation.database.action" value="create"/>
        </properties>
    </persistence-unit>
</persistence>
```

### Using Docker to run PostgreSQL

You should have docker installed locally. If you don't have, you can get it at: https://www.docker.com/

Run PostgreSQL:

```shell
docker run --name postgres-instance -p 5432:5432 -e POSTGRES_PASSWORD=password -e POSTGRES_DB=mydb -d postgres
```

### Running your application

To test your application, you can package it with Maven, run with Java, and use Postman, curl, or other tool of preference to invoke the rest APIs. 

* To package your application:

    ```bash
    $ mvn clean package
    ```

* To run your application: 

    ```shell
    java -jar -Dserver.host=localhost -Dserver.database=mydb -Dserver.user=postgres -Dserver.password=password -Djava.net.preferIPv4Stack=true target/acme-store-service.jar
    ```

* By default it will run on port 8080. So you should be able to test the application with the following request examples:
    * Insert a new product

        ```shell
        curl --location --request POST 'http://localhost:8080/products' \
        --header 'Content-Type: application/json' \
        --data-raw '{"name": "bottle", "description": "Can store cold and hot liquids.", "quantity": "2"}'
        ```
    
    * List all products
  
        ```shell
        curl --location --request GET 'http://localhost:8080/products'
        ```
      
    * Search a product by ID (name)
  
        ```shell
        curl --location --request GET 'http://localhost:8080/products/bottle'
        ```
      
    * Update a product using its ID (name)
  
        ```shell
        curl --location --request POST 'http://localhost:8080/products/bottle' \
        --header 'Content-Type: application/json' \
        --data-raw '{"name": "bottle", "description": "Can store ONLY cold liquids.", "quantity": "2"}'
        ```

    * Delete a product by ID (name)
  
        ```shell
        curl --location --request DELETE 'http://localhost:8080/products/bottle'
        ```

If you can successfully execute the above requests, it means your endpoints are working! The next step is to try data that actually breaks the business rules and validate if your application is validating for example, the required fields.

!!! success "Congratulations!"

    You've finished your first task in the Acme Store! You have created the base project that will be later used to persist the e-commerce data using a database, will have properly documented APIs, and much more! We're looking forward to have you helping  with the next task!
