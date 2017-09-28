---
layout: post
title: Security on object level with Spring ACL  —  Part 1
---

In this series of 3 parts we’ll be getting to know a little bit more about Spring ACL, and by the end of it you’ll have a fully functional sample using Spring Boot with Spring Security and Spring ACL with all it’s objects very safe. :D
### What the heck is Spring ACL?
Spring ACL is a part of the spring ecosystem that takes care of Domain Object Security, it guards every tiny Domain Object, according to some specified rules, leaving the Authentication and the Method Invocation security to the Spring Security.
The key concepts, and also the database tables, that one have to know to deal with Spring ACL are:

 - Security IDentity (SID): It’s the identifier and can be a user or group;
 - Object Identity: Keeps information about each Domain Object on the system.
 - Access Control Entity (ACE): It’s the bond between domain objects, permissions and SID’s.
 - Class: The identification of a domain object class. Simple as that.

The diagram below shows how is the schema and how would the parts be populated.


![Spring ACL populated schema](https://raw.githubusercontent.com/samuelbwr/samuelbwr.github.io/master/images/spring-acl/explain-diagram.png)


Enough of words, let’s dive in!

For the following example I’m assuming you already now a little bit of Java and Spring Boot.
We’ll be using:

 - Spring Boot 1.5.7 (latest now)
 - Spring Boot JPA
 - Spring ACL 4.2.3
 - H2 Embedded DB
 - Maven

For simplicity and shortness sake I’ll be posting here only the very necessary code, but all the code can be found in https://github.com/samuelbwr/Spring-ACL/tree/master/Spring-ACL-Part-1.

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
  </dependency>
  <dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-acl</artifactId>
    <version>4.2.3.RELEASE</version>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```
The ``AclConfig.java`` will look like this:
```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true)
public class AclConfig extends GlobalMethodSecurityConfiguration {

    @Autowired
    DataSource dataSource;

    AuditLogger auditLogger = new ConsoleAuditLogger();

    @Bean
    JdbcMutableAclService aclService() {
        return new JdbcMutableAclService( dataSource, lookupStrategy(), aclCache() );
    }

    LookupStrategy lookupStrategy() {
        return new BasicLookupStrategy( dataSource,
                aclCache(), new AclAuthorizationStrategyImpl( new SimpleGrantedAuthority( "ROLE_ADMINISTRATOR" ) ), auditLogger );
    }

    private SpringCacheBasedAclCache aclCache() {
        return new SpringCacheBasedAclCache(
                new ConcurrentMapCache( "cache" ),
                new DefaultPermissionGrantingStrategy( auditLogger ),
                new AclAuthorizationStrategyImpl( new SimpleGrantedAuthority( "ROLE_ACL_ADMIN" ) ) );
    }

    @Override
    protected MethodSecurityExpressionHandler createExpressionHandler() {
        org.springframework.security.acls.model.AclService service = aclService();
        DefaultMethodSecurityExpressionHandler expressionHandler =
                new DefaultMethodSecurityExpressionHandler();
        expressionHandler.setPermissionEvaluator( new AclPermissionEvaluator( service ) );
        expressionHandler.setPermissionCacheOptimizer( new AclPermissionCacheOptimizer( service ) );
        return expressionHandler;
    }
}
```
