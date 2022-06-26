# Golden service apis for ACME Store       

In this exercise you can validate and practice your knowledge of the following concepts:

* Java microservice implementation with Helidon; 
* API designing to meet specific criteria;
* RESTful services implementation with JAX-RS;
* Extense API documentation with OpenAPI;
* In-memory persistence with MicroStream;
* Validation with Bean Validation;
* Implicit and constant usage of CDI;

Getting familiar with the use case and the exercise goals described next is highly recommended, as a detailed 
step-by-step guide is not provided. 

**Friendly advice**: consider leveraging this opportunity to 
upskill by making the best use of your knowledge and research skills to code the solution without replicating the 
provided solution.

## Scenario

Your consultancy was requested by the Acme Store company. The company's technical team wants to learn good 
practices and recommendations for Java microservices built on top of the building blocks offered by specifications. 
They demonstrate interest in validating the experience of delivering Microprofile on top of the Helidon 
runtime. 

Your task is to provide a Java backend service, with Helidon, to handle the basic operations of the store's products . 

### Goals

See below the solution's pre-requisites. (_Easily track your progress by marking tasks as "done"._)

- [ ] The service should be named `acme-store-rest`. It must rely on *Microprofile 3.3* and *Helidon*.
- The service handles operations for `Products` maintenance. A `Product` should have:
    - [ ] An ID, which is the `name`: mandatory and should be at max 100 chars.
    - [ ] A `description`:  mandatory, should have at least 5 chars
    - [ ] A `quantity`: mandatory and should be higher than 0. 
- The following operations for a `Product` should be available:
    - [ ] List all the products
    - [ ] Insert a new product
    - [ ] Retrieve a product by ID
    - [ ] Update a product based on its ID
    - [ ] Delete a product using its ID
- It should be a RESTful application. The urls should follow these rules:
    - [ ] To list all products:  GET "/products"
    - [ ] To insert a product: POST "/products" 
    - [ ] To find a product by ID: GET "/products/{productName}"
    - [ ] To delete a product: DELETE "/products/{productName}"
    - [ ] To update a product: PUT "/products/{productName}"
- The APIs should be properly documented:
  - [ ] All APIs should have documentation for the `Operation`, including `summary` and `description`;
  - [ ] The responses (`APIResponse`) should be documented according to potential results, with proper 
    `description` for returned HTTP codes (`responseCode`, e.g. 200, 404).
- The APIs should include information about release and stability through tags:
  - The following apis are part of the first release, and tagged (`@Tag`) as `1.0`:
    - [ ] List all
    - [ ] Find by ID
    - [ ] Save new product
  - These APIs are still in BETA phase, and are tagged (`@Tag`) as `BETA`:
    - [ ] Update 
    - [ ] Delete
 
## Hands-on time: implementing the project

To save you time, the customer's team has created a basic project using the [microprofile starter](https://start.
microprofile.io/) and they have provided some domain related objects and boilerplate code. 

1. Get started by cloning the project to your machine (if you haven't already). 
```
git clone https://github.com/architects4j/helidon-microstream-training-labs-foundation
cd acme-store-rest
```
2. Open the project in your IDE of choice and give it an overall look. You can find multiple comments pointing to 
   code that needs adjustment.
3. If you are feeling confident, go ahead and get started! If you need some extra help, check the following section. 

## Tips and hints

### Creating the project

2. You will need to create the POJO `Product.java` with the described attributes.

3. You don't need database persistence. You can use in-memory repository. To get started faster, you can create a `ProductRepository` interface, and an implementation of it, the `ProductRepositoryMemory`. You can use the [RestaurantRepository](https://github.com/otaviojava/payara-fastlane-demos/blob/main/restaurant/src/main/java/my/compary/restaurant/RestaurantRepository.java) and [RestaurantRepositoryMemory](https://github.com/architects4j/helidon-microstream-training-demos/blob/main/restaurant/src/main/java/com/otaviojava/workshop/microstream/helidon/restaurant/RestaurantRepositoryMemory.java) as an inspiration.  

    !!! TIP
    
        Notice the CDI scope defined for the [RestaurantRepositoryMemory](https://github.com/architects4j/helidon-microstream-training-demos/blob/0af41f1242f4fb37c538829d8291e85250d82a89/restaurant/src/main/java/com/otaviojava/workshop/microstream/helidon/restaurant/RestaurantRepositoryMemory.java#L11) bean. 

4. You will need a `ProductController` to implement your REST APIs:
    1. To best fit this application need, it can be configured with `@RequestScope`.
    5. Remember to set the `javax.ws.rs.@Path` according to the goals of the project;
    1. You can use `@Inject` to use your `ProductRepository` and manipulate the data.
    2. Make sure to avoid any exceptions in case an invalid data is sent to your endpoints.
    3. To differenciate your APIs, you can annotate your methods with: `@DELETE`, `@PUT`,  `@POST` and `@GET` from `javax.ws.rs`.
    4. To create an endpoint like `/products/{productName}`, you can annotate the respective method with `@Path("{id}")` and make sure you add a @pathParam to your method. Example:

        ```java
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

### Running your application

* To test your application, you can package it with Maven, run with Java, and use Postman, curl, or other tool of preference to invoke the rest APIs. 

* To package your application:

  ```bash
  $ mvn clean package
  ```

* To run your application: 

  ```shell
  java -jar target/acme-store-service.jar
  ```
* or with helidon cli:

    ```
    helidon dev
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

    You've finished your first task in the Acme Store! You have created the base project that will be later used to persist the e-commerce data using a database, will have properly documented APIs, and much more! We're looking forward to seeing you upskilling by working on upcoming challenges!
