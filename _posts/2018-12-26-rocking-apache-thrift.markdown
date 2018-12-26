---
layout: post
title:  "Spring-boot and Apache Thrift for microservices!"
date:   2018-12-26 22:40:54
categories: java thrift backend spring-boot
tags: backend microservices
excerpt: How to build a Apache thrift enabled Spring-boot microservice application
mathjax: true
---

Are you building backend for the next billion then you might already know what microservices are and how it can help use break through the old monolithic codebase.

**So what is Apache Thrift exactly?**

A formal definition would be *The Apache Thrift software framework, for scalable cross-language services development, combines a software stack with a code generation engine to build services that work efficiently and seamlessly between C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, OCaml and Delphi and other languages.* This was okay but it doesn't really make us want to use it. What is really important is convenient interoperable RPC invocations and convenient handling of binary data.

Now what I mean by this is that you can make consistent calls with a 100% confidence and it also make generates both sever side and client side code for you to implement.

So let us not waste any time and get into the code.

First off we start with the basic configuration of the *spring-boot* pom.xml file which will have all our configuration, libraries we require and everything else like dependency.

pom.xml
```xml 
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <parent>
            <!-- Your own application should inherit from spring-boot-starter-parent -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-samples</artifactId>
            <version>${revision}</version>
        </parent>
        <artifactId>spring-boot-sample-data-jpa</artifactId>
        <name>Spring Boot Data JPA Sample</name>
        <description>Spring Boot Data JPA Sample</description>
        <properties>
            <main.basedir>${basedir}/../..</main.basedir>
        </properties>
        <dependencies>
            <!-- Compile -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-data-jpa</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <!-- Runtime -->
            <dependency>
                <groupId>com.h2database</groupId>
                <artifactId>h2</artifactId>
                <scope>runtime</scope>
            </dependency>
            <!-- Test -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <configuration>
                        <fork>true</fork>
                        <mainClass>com.oyo.acp.EventsApplication</mainClass>
                        <outputDirectory>./libs</outputDirectory>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-dependency-plugin</artifactId>
                    <version>2.10</version>
                    <executions>
                        <execution>
                            <id>unpack-zip</id>
                            <phase>prepare-package</phase>
                            <goals>
                                <goal>unpack-dependencies</goal>
                            </goals>
                            <configuration>
                                <includeGroupIds>com.newrelic.agent.java</includeGroupIds>
                                <includeArtifactIds>newrelic-java</includeArtifactIds>
                                <outputDirectory>./libs</outputDirectory>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                </plugin>

                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>build-helper-maven-plugin</artifactId>
                    <executions>
                        <execution>
                            <id>add-source</id>
                            <phase>generate-sources</phase>
                            <goals>
                                <goal>add-source</goal>
                            </goals>
                            <configuration>
                                <sources>
                                    <source>target/generated-sources/gen-javabean</source>
                                </sources>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>
                <plugin>
                    <artifactId>maven-antrun-plugin</artifactId>
                    <configuration />
                    <executions>
                        <execution>
                            <id>generate-sources</id>
                            <phase>generate-sources</phase>
                            <configuration>
                                <target>
                                    <echo>create (or clear) output directory for generated files</echo>
                                    <mkdir dir="${gendir.target}" />
                                    <delete>
                                        <fileset dir="${gendir.target}" includes="**/*" />
                                    </delete>
                                    <echo>generate java source files to ${gendir.target}
                                        from
                                        ${thrift.interface}
                                    </echo>
                                    <exec executable="${thrift.exe}">
                                        <arg value="-r" />
                                        <arg value="--gen" />
                                        <arg value="java:beans" />
                                        <arg value="-o" />
                                        <arg value="${gendir.target}" />
                                        <arg value="${thrift.interface}" />
                                    </exec>
                                </target>
                            </configuration>
                            <goals>
                                <goal>run</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
        </plugins>
        </build>
    </project>
```

Let's try to understand what's going on here
The idea is to take a Thrift file put in the desired directory and let maven do it's job of building all the code and putting in repo for us.
Remember to install thrift beforehand.
Simple run **brew install thrift** and you should be set.

Let's write a simple Thrift file.
put in src/main/thrift (create a new directory under main named thrift and name the file as **main.thrift**)

```thrift

exception InvalidOperationException {
    1: i32 code,
    2: string description
}
 
service CrossPlatformService {
 
    bool ping() throws (1:InvalidOperationException e)
}

```
This is a simple example of thift code which pings the server to test connection and returns an exception otherwise.

Assuming you are in your Spring project directory with a main class already written.
let's compile the source with **mvn clean compile** and all the files should be in place.

This should produce few java files inside *generated-files* directory.

Now let's get to some implementation.
As you might open the huge java thrift generated file. You will see something called the fuction Iface which is our starting point for implementing this whole thrift thing.


now let's move to src directory where you might see the Application.java or our SpringApp.

So our first file to create a Handler and Service to handle all the fuctions we have in our thrift file in this case *ping()*.

create a class *ThriftFunctionService.java*

```java

package groupid.artifactid;
// put all the related libraries here ...


// here we are going to implement all the thrift services ..
public class ThriftFunctionService implements CrossPlatformService.Iface {

    // this is our function which we earlier set which also throws some exception 
    @Override 
    public boolean ping() throws InvalidOperationException, TException {
        // only if we can conect will return true ... 
        return true;
    }

}
```

now we all set to write the final peice of server and we are set to go ..

Open up the main *springApp.java* file, here we need to add a few beans to Serialize the data and to do the connection.

```java 
@Bean
public TProtocolFactory tProtocolFactory() {
    //We will use binary protocol, but it's possible to use JSON and few others as well
    return new TBinaryProtocol.Factory();
}
@Bean
public Servlet calculator(TProtocolFactory protocolFactory, CalculatorServiceHandler handler) {
    return new TServlet(new TCalculatorService.Processor<CalculatorServiceHandler>(handler), protocolFactory);
    return new ServletRegistrationBean(tServlet, "/ping");
}
```
And we are done writing our server 

**Let's write our client ...**

To build the client we just need the thrift file and the same pom file 
and **mvn clean compile**
this will generate all th necessary code and files..

Writing the client is simple 

just add a bean to the main springApp in the client
```java
@Autowired
protected TProtocolFactory protocolFactory;
@Value("${local.server.port}")
protected int port;
@Bean
public void setUp() throws Exception {
    TTransport transport = new THttpClient("http://localhost:" + port + "/"); // modify as required
    TProtocol protocol = CrossPlatformService.getProtocol(transport);
    client = new CrossPlatformService.Client(protocol);
    System.out.print(client.ping()); // call the required fuction from the thrift file.
}
```
and we are set to go 

run both the Server and client with **mvn spring-boot:run**

you might see *true* or *1* in your console;

That's it you have your simple microservice ready in action.
