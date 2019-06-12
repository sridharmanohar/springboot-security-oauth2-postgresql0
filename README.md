## Example Spring Boot + Hibernate + Spring Security + OAuth2 project for demonstration purposes.
1. This example is from https://dzone.com/articles/secure-spring-rest-with-spring-security-and-oauth2  
2. This example uses postgresql as the token store for oauth2  
3. Also provides an option to configre H2 as an in-memory database for the oauth2 token store  
4. I will be looking at only the postgresql option though  
5. This example also uses password grant flow  
6. The flow and various scenarios, executions and their results are well documented in the Program Flow, Issues and Changes document. Please refer.  
6. All other details that explains the main classes, interfaces and other things that are related to spring security + oauth2 implementation are exlained and documented as part of springboot-security-oauth2-postgresql1 project. Please refer it's word documentation.  


## Getting started
### Prerequisites:
- Java 8
- Maven
- H2/PostgreSQL

It is possible to run application in one of two profiles:
- h2
- postgres

depending on database engine chose for testing. 

To enable cache statistics `dev` profile needs to be turned on.

### Testing database schema
![database-schema](src/main/docs/db_schema.png)

### Authentication

```
curl -X POST \
  http://localhost:8080/oauth/token \
  -H 'authorization: Basic c3ByaW5nLXNlY3VyaXR5LW9hdXRoMi1yZWFkLXdyaXRlLWNsaWVudDpzcHJpbmctc2VjdXJpdHktb2F1dGgyLXJlYWQtd3JpdGUtY2xpZW50LXBhc3N3b3JkMTIzNA==' \
  -F grant_type=password \
  -F username=admin \
  -F password=admin1234 \
  -F client_id=spring-security-oauth2-read-write-client
```

### Accessing secured endpoints

```
curl -X GET \
  http://localhost:8080/secured/company/ \
  -H 'authorization: Bearer e6631caa-bcf9-433c-8e54-3511fa55816d'
```