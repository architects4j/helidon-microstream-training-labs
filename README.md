Access the labs at: https://architects4j.github.io/helidon-microstream-training-labs/

# Labs and exercises

* [CDI - Context and Dependency Injection](cdi-lab.md)
* [Getting started with Helidon](payara-getting-started.md)
* [Acme Store - Java RESTFul service with Helidon](challenge-rest.md)
* [RestClient](rest-client-lab.md)

# About this Course:

## Helidon & MicroProfile Fundamentals (Labs)

In this course, you will learn the fundamentals of how to develop modern cloud-native microservices with Helidon and MicroProfile.

MicroProfile is a specification for building microservices architecture. MicroProfile was derived from Jakarta EE, formerly Java EE, and thus allows developers to migrate and reuse their existing Java EE code seamlessly in modern microservice applications. It delivers application portability of microservices across multiple MicroProfile vendor frameworks such as Helidon, Helidon, Quarkus, Open Liberty, and TomEE.

---
pip3 install mkdocs-enumerate-headings-plugin
enumerate-headings ->

## Contributing to this project

The training labs are based on mkdocs.

Env setup: 

```
  pip install mkdocs
  pip install -e mkdocs-material
  pip install mkdocs-awesome-pages-plugin
  pip install mkdocs-enumerate-headings-plugin
```

Development: 

After cloning this repo and during development check the changes. It will start the live-reloading docs server.

```
mkdocs serve
````

Publish:
mkdocs gh-deploy --force



