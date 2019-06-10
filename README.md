# [Spring Boot + Spring Security + OAuth2](https://dzone.com/articles/secure-spring-rest-with-spring-security-and-oauth2)

Example Spring Boot + Hibernate + Spring Security + OAuth2 project for demonstration purposes.  
this is from https://dzone.com/articles/secure-spring-rest-with-spring-security-and-oauth2 

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

## Example Spring Boot + Hibernate + Spring Security + OAuth2 project for demonstration purposes.
1. This example is from https://dzone.com/articles/secure-spring-rest-with-spring-security-and-oauth2  
2. This example uses postgresql as the token store for oauth2  
3. Also provides an option to configre H2 as an in-memory database for the oauth2 token store  
4. I will be looking at only the postgresql option though  
4. This example also uses password grant flow  
5. **Project Structure**
    * java  
        * com.adamzareba.spring.security.oauth2  
            * **Application**    
                * Main Application class  
                * Annotations Used:
                    * class level: 
                        * @SpringBootApplication(exclude = JmxAutoConfiguration.class) ** what is this JmxAutoConfiguration??**    
        * com.adamzareba.spring.security.oauth2.config         
            * **HibernateConfiguration**   
                * **what is this class about?? why is it even required??**  
                * Methods:  
                    * public Module hibernate5Module()  
                        * returns a new instance of Hibernate5Module **what is this thing??**  
                        * configured as @Bean  
                    * public PlatformTransactionManager transactionManager(EntityManagerFactory emf)  
                        * takes EntityManagerFactory **what is this??**  
                        * instantiates JpaTransactionManager **what is this??**  
                        * and sets the passed in EntityManagerFactory instance in JpaTransactionManager  
                        * and returns an of JpaTransactionManager  
                        * **why is all this required??**  
                        * also annotated as @Bean  
                        * **and what is PlatformTransactionManager??**  
                    * public PersistenceExceptionTranslationPostProcessor exceptionTranslation()  
                        * just returns a new instance of PersistenceExceptionTranslationPostProcessor  
                        * **what is this all about??**  
                * **i don't understand why is this class even required??**      
                * Annotations Used:  
                    * class-level:   
                        * @Configuration, @EnableTransactionManagement  
                    * method-level: 
                        * @Bean   
        * com.adamzareba.spring.security.oauth2.config.encryption  
            * **Encoders**  
                * Methods:  
                    * public PasswordEncoder oauthClientPasswordEncoder()  
                        * returns a new instance of BCryptPasswordEncoder with 4 as an argument  
                        * annot. as @Bean  
                        * this is the password for the client which has been hashed 4 rounds with bcrypt  
                    * public PasswordEncoder userPasswordEncoder()  
                        * returns a new instance of BCryptPasswordEncoder with 8 as an argument  
                        * annot. as @Bean  
                        * and user password hashed 8 rounds with bcrypt  
                * **what is exactly happening in this class??**  
                * **the content of this class, could it have been merged with some other class??**  
                * Annot. Used:  
                    * Class-level:  
                        * @Configuration  
                    * Method-level:  
                        * @Bean     
        * com.adamzareba.spring.security.oauth2.config.profile.annotation  
            * **Dev**  
                * this is an interface annotation    
                * Annot. Used:  
                    * interface-level:  
                        * @Target(ElementType.TYPE)  
                        * @Retention(RetentionPolicy.RUNTIME)  
                        * @Profile("dev")  
                        * **what is the significance of these annotations??**  
                * **what does this annot. really do??**                 
        * com.adamzareba.spring.security.oauth2.config.server  
            * **AuthServerOAuth2Config**  
                * class extends AuthorizationServerConfigurerAdapter 
                * DataSource is DI'd  **what is this - class or interface??**  
                * AuthenticationManager is DI'd  
                * UserDetailsService is DI'd **why is this here?? how is it diff from the impl. in oauth2+non-jwt+inmemory codebase??**   
                * PasswordEncoder is DI'd  
                    * **i believe the instance name is important because there are two PasswordEncoder bean defs??**  
                * Methods:  
                    * public TokenStore tokenStore()  
                        * returns a new instance of JdbcTokenStore with the DI'd DataSource instance  
                        * **what is JdbcTokenStore??**  
                        * annot. as @Bean  
                        * **why isnt this just DI'd like how we did in oauth2+non-jwt+inmemory codebase??**  
                    * public OAuth2AccessDeniedHandler oauthAccessDeniedHandler() 
                        * returns a new instance of OAuth2AccessDeniedHandler  
                        * **why is this handled it like this and not like oauth2+non-jwt+inmemory codebase??**  
                        * annot. as @Bean  
                    * public void configure(AuthorizationServerSecurityConfigurer oauthServer)  
                        * this is an overridden method   
                        * **did we configure AuthorizationServerSecurityConfigurer in oauth2+non-jwt+inmemory codebase??**  
                        * **what does this do??**  
                        * calls tokenKeyAccess and sets permitAll **what is this??**  
                        * calls checkTokenAccess and says isAuthenticated() **is this all required??**  
                        * and sets the passwordEncoder to the DI'd PasswordEncoder  
                    * public void configure(ClientDetailsServiceConfigurer clients)  
                        * this is an overridden method   
                        * configures the client details  
                        * if it were an in-memory config, we would be calling the inmemory, **what is that??**  
                        * calls jdbc and sets the DI'd dataSource  
                        * **where is this dataSource configured??**   
                    * public void configure(AuthorizationServerEndpointsConfigurer endpoints)  
                        * also an overridden method  
                        * **what is this method?? and how diff is it from the one at oauth2+non-jwt+inmemory codebase??**    
                * Annot. Used:  
                    * Class-level:  
                        * @Configuration, @EnableAuthorizationServer, @EnableGlobalMethodSecurity(prePostEnabled = true) **where is this even used??**, @Import(ServerSecurityConfig.class) **what is this??**  
                    * method-level:  
                        * @Autowired, @Qualifier("dataSource") **what is the purpose??**, @Bean, @Override         
            * **ResourceServerConfiguration**  
                *  extends ResourceServerConfigurerAdapter  
                *  methods:  
                    * public void configure(ResourceServerSecurityConfigurer resources)  
                        * sets the resource id **purpose??**  
                            * this is just like naming the resource server, not needed   
                        * this is a overridden method  
                    * public void configure(HttpSecurity http) throws Exception  
                        * overridden method  
                        * **what is requestMatchers? why dint we use it in oauth2+non-jwt+inmemory codebase??**    
                        * **what is happening in this method??**  
                * Annot. used:  
                    * class-level:  
                        * @Configuration, @EnableResourceServer  
                    * method-level:   
                        * @Override     
            * **ServerSecurityConfig**  
                * extends WebSecurityConfigurerAdapter  
                * UserDetailsService is DI'd  
                * PasswordEncoder is Di'd **again, I believe the instance name plays a crucial name here**  
                * Methods:  
                    * public AuthenticationManager authenticationManagerBean() throws Exception  
                        * calls super  
                        * this is overridden method  
                        * and annot. as @Bean
                    * protected void configure(AuthenticationManagerBuilder auth) throws Exception  
                        * configures UserDetailsService by passing in DI'd UserDetailsService  
                        * and configures PasswordEncoder  
                        * this is also a overridden method  
                * Annot. used:  
                    * class-level:  
                        * @Configuration, @EnableWebSecurity, @Order(SecurityProperties.ACCESS_OVERRIDE_ORDER) **what is this for??**, @Import(Encoders.class) **what is this??**  
                    * method-level:  
                        * @Override, @Bean  
                    * instance-level:  
                        * @Autowired           
        * com.adamzareba.spring.security.oauth2.controller  
            * **CompanyController**  
                * CompanyService is DI'd. This is business centric service interface.    
                *  has methods to fetch All companies, fetch by name, by id, to create, update and delete a company  
            * Annot. used:  
                * class-level:   
                    * @RestController, @RequestMapping("/secured/company") **what is this??**  
                * method-level:  
                    * @RequestMapping(method = RequestMethod.GET, produces = MediaType.APPLICATION_JSON_VALUE)  
                    * @ResponseStatus(value = HttpStatus.OK)  
                    * @RequestMapping(method = RequestMethod.POST, produces = MediaType.APPLICATION_JSON_VALUE)  
                    * @RequestMapping(method = RequestMethod.PUT, produces = MediaType.APPLICATION_JSON_VALUE)  
                    * @RequestMapping(value = "/{id}", method = RequestMethod.DELETE, produces = MediaType.APPLICATION_JSON_VALUE)  
                    * **what is this produces??**  
                    * **what is the diff between GET, POST, PUT, DELETE??**  
        * com.adamzareba.spring.security.oauth2.model  
            * **Address**  
                * this is a model/pojo.  
                * has id, street, housenumber and zipcode as its members  
                * uses lombok, so no setters and getters  
                * **why is the primary key field set to null??**  
                * Annot. used:  
                    * class-level:  
                        * @Entity, @Table(name = "ADDRESS"), @Getter, @Setter  
                    * instance-level:  
                        * @Id, @GeneratedValue(strategy = GenerationType.AUTO), @Column            
            * **Car**  
                * this is a model/pojo  
                * has id, reg number and a company  
                * Annot. used:  
                    * class-level:  
                        * @Entity, @Table(name = "CAR"), @Getter, @Setter  
                    * instance-level:  
                        * @Id, @GeneratedValue(strategy = GenerationType.AUTO), @Column, @ManyToOne(fetch = FetchType.LAZY), @JsonBackReference **what is this??**              
            * **Company**  
                * this is a model/pojo  
                * has id, name, Set of departments, Set of Cars  
                * Annot. used:  
                    * class-level:  
                        * @Entity, @Table(name = "COMPANY", uniqueConstraints = {@UniqueConstraint(columnNames = {"NAME"})}), @Getter, @Setter  **what is this uniqueconstraint about??**  
                    * instance-level:  
                        * @Id, @GeneratedValue(strategy = GenerationType.AUTO), @Column, @OneToMany(cascade = CascadeType.ALL, mappedBy = "company", fetch = FetchType.LAZY, orphanRemoval = true), @JsonManagedReference **what is this??**              
            * **Department**  
                * model/pojo  
                * has id, name, set of employees, set of offices and a company  
                * Annot. used:  
                    * class-level:  
                        * @Entity, @Table(name = "DEPARTMENT"), @Getter, @Setter  
                    * instance-level:  
                        * @Id, @GeneratedValue(strategy = GenerationType.AUTO), @Column, @OneToMany(cascade = CascadeType.ALL, mappedBy = "department", fetch = FetchType.LAZY, orphanRemoval = true), @JsonManagedReference, @JsonBackReference **what is this??**              
            * **Employee**  
                * This is an Employee entity/pojo.  
                * It has an Id, Name, Surname, an Address and is also associated with a Department.  
                * annotations - nothing new 
            * **Office**  
                * This is an Office entity/model/pojo  
                * It has an Id, Name, Address and is also associated with a Department  
                * annotations - nothing new  
        * com.adamzareba.spring.security.oauth2.model.security  
            * **Authority**  
                * this class implements GrantedAuthority  
                * And it has Id and Name  
                * Methods:  
                    * public String getAuthority()  
                        * this is a overridden method of GrantedAuthority  
                * **why is this class even required?**          
            * **User**  
                * class implements UserDetails **but why??**   
                * And it has Id, Username, Password, AccountExpired, AccountLocked, CredsExpired, Enabled attributes and each User can have multiple Authorities   
                * Annot. Used:  
                    * class-level:  
                        * @Entity, @Table(name = "USER_", uniqueConstraints = { @UniqueConstraint(columnNames = { "USER_NAME" }) }), @Getter, @Setter, @EqualsAndHashCode(of = "id") **what is this??**    
                    * instance-level:  
                        * @Id, @GeneratedValue(strategy = GenerationType.IDENTITY), @Column, @OrderBy **what is this??**, @JsonIgnore **is this same as unknown=true??**      
        * com.adamzareba.spring.security.oauth2.repository  
            * **CompanyRepository**  
                * This is an interface and has declared methods to find, update, create etc.  
                * Purely for business purposes.   
            * **CompanyRepositoryImpl**  
                * This class implements CompanyRepository interface  
                * And hence it overrides and provides implementation for all the methods defined in the interface  
                * Not going into its details   
            * **UserRepository**  
                * This interface extends JpaRepostory and provides User entity  
                * Since we want the User details and their authorities to be fetched from the db, we need to tell spring how to fetch them and this is what tells how to fetch that data.
                * has one method - findByUsername   
                * and the query to find the user is jql   
                * Annot. used:  
                    * class-level:  
                        * @Repository  
                    * method-level:  
                        * @Query     
        * com.adamzareba.spring.security.oauth2.service  
            * **CompanyService**  
                * this is an interface  
                * has methods to fetch, create, delete companies  
                * but the things is it looks really very dummy to me since the CompanyRepository interface already has the same kind of methods and this just looks like a facade to me   
            * **CompanyServiceImpl**  
                *  This class implements CompanyService interface  
                *  hence it overriddes all its methods and then calls the CompanyRepository interface methods to fetch data from the db  
                *  Ofcourse, CompanyRepository is DI'd  
                *  but, i think, this class is unnecessary. Rather the CompanyService interface could be made into a class and we can just call the CompanyRepo. from there itself  
                *  Anyways, the @EnableGlobalMethodSecurity(prePostEnabled = true) which was used in Auth.ServerConfig class is made use of here by using @PreAuthorize annot  
                *  And secured the service layer methods further by ensuring that users with correct authority can only access the methods  
                *  Annotations USed:  
                    * class-level:  
                        * @Service  
                    * method-level:  
                        * @Override  
                        * @Transactional(readOnly = true)  
                        * @PreAuthorize("hasAuthority('COMPANY_READ') and hasAuthority('DEPARTMENT_READ')")  
                            * **din't know that even hasAuthority can be used??**   
                            * **so apart from hasRole and hasAuthority, what all can be used in @PreAuthorize annot??**  
            * **UserDetailsServiceImpl**  
                * this implements UserDetailsService interface  
                * and provides implementation for its one and only method - loadUserByUsername  
                * Since we need to fetch User details and their authorities from db, we need to tell spring how to fetch them and we do it by implementing the UserDetailsService interface  
                * UserRepository is DI'd and this repos findByUsername is called to provide implementation for this method  
                * Annot. used:  
                    * class-level:  
                        * @Service  
                    * method-level:  
                        * @Override, @Transactional(readOnly = true) **why is this required??**   
                    * instance-level:  
                        * @Autowired        
    * **resources**  
        * db/migration/postgresql  
            * V1__create_hibernate_sequence  
                * this creates a sequence  
                * **but why is this required, can't we leave it to the db??**   
            * V2__create_address  
                * creates address table  
                * for pk we use bigint in postgresql to sync with Long in java   
            * V3__create_company  
            * V4__create_car  
            * V5__create_department  
            * V6__create_employee  
            * V7__insert_car  
            * V8__create_office  
            * V9__insert_office  
            * V10__create_oauth2  
                * creates 6 tables:  
                    * OAUTH_CLIENT_DETAILS  
                        * client_id is the pk  
                        * and mainly conatins the client id, secret, access and refresh tokens validities, scope and  grant types that each client is assigned   
                    * OAUTH_CLIENT_TOKEN  
                        * authentication id is the pk  
                        * **what is the use of this table, have seen no entry in this??**   
                    * OAUTH_ACCESS_TOKEN  
                        * access token gets stored in here, ofcourse in encrypted form  
                        * the token is stored in a column with datatype bytea **what is this??**  
                        * also stores a ref. to refresh token, not a fk, but the static value. I think it internally manages but explcitly we are not required to create any fk.   
                        * these tokens are never deleted from the table even after expiry. Btw, expiry is calculated on the go.  
                    * OAUTH_REFRESH_TOKEN  
                        * refresh token is stored in this  
                        * again this is also stored in a column with bytea **what is this??**   
                        * these tokens are never deleted from the table even after expiry.  
                    * OAUTH_CODE  
                        * **what is this table used for??**
                    * OAUTH_APPROVALS    
                    * **what is this table used for??**  
            * V11__create_user  
                * creates a user, authority and user authorities table  
            * V12__insert_authentication  
                * inserts clients and their details in OAUTH_CLIENT_DETAILS table  
            * V13__insert_authorities  
                * inserts authorities like COMPANY_CREATE, COMPANY_UPDATE etc.  
            * V14__insert_user  
                * inserts Users into USER_ table and also maps entries into user authorities table          
        * application.properties  
            * spring.profiles.active=dev, postgres **what does this do??**   
        * application-postgres.properties  
            * **what is flyway used for??**     
6. **Maven Details**
    * spring-boot-starter-parent maps to version 1.5.9.RELEASE  
    * The following are all the dependencies:  
        * spring-boot-starter-web  
        * spring-boot-starter-data-jpa  
        * spring-boot-starter-security  
        * spring-context-support **why is this required??**  
        * spring-hateoas **what is this??**  
        * h2  
        * postgresql  
        * hibernate-jpamodelgen  
        * flyway-core  
        * jackson-databind  
        * jackson-datatype-hibernate5 **what is this for??**  
        * spring-security-oauth2  
        * lombok  
        * spring-boot-starter-test  
        * spring-boot-devtools - this i have added for live-reloads  
 