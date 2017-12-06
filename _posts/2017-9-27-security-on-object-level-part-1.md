---
layout: post
title: Security on object level with Spring ACL  —  Part 1
---

In this series of 2 parts we’ll be getting to know a little bit more about Spring ACL, and by the end of it you’ll have a fully functional sample using Spring Boot with Spring Security and Spring ACL with all it’s objects very safe. :D



### What is ACL?

ACL is the acronym for Access Control List and it's vastly used in File System and Networking security. One of the popular uses of it is on AWS S3.

As the name states ACL isn't much more than a list, a list that keeps a directory, file, network or anything of it's use and some user's permission to it, basically.

If we take the AWS S3 as example, we can enter in a bucket page and access the Permissions tab to view the page below.

![AWS S3 Permissions](https://raw.githubusercontent.com/samuelbwr/samuelbwr.github.io/master/images/spring-acl/acl.png)

It just don't go far from that. The account, the bucket itself and the permission. Plain simple.

Of course, you can specify a more complex approach on the Bucket Policy, but that's not required.

### How Spring implements ACL?
Spring ACL is a part of the Spring ecosystem that takes care of Domain Object Security, it guards every tiny Domain Object, according to some specified rules, leaving the Authentication and the Method Invocation security to the Spring Security.

Spring ACL apply a more Object-Oriented approach to the ACL's, dividing Identifiers ( User or Group ) and the Domain Object's into two separate classes, and uniting them in another one.

The key concepts, and also the database tables, that one have to know to deal with Spring ACL are:

 - **S**ecurity **ID**entity (SID): It’s the identifier and can be a user or group;
 - Object Identity: Keeps information about each Domain Object on the system.
 - **A**ccess **C**ontrol **E**ntity (ACE): It’s the bond between domain objects, permissions and SID’s.
 - Class: The identification of a domain object class. Simple as that.

The diagram below shows how is the schema and how would the parts be populated.


![Spring ACL populated schema](https://raw.githubusercontent.com/samuelbwr/samuelbwr.github.io/master/images/spring-acl/explain-diagram.png)

Now that we now how ACL and Spring ACL works we are ready to code. And we'll do that on the second part of this series. We're going to find out how to customize Spring ACL to it's best use and move some things around to give to some flexibility.



