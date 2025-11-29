Integrating JAX-WS with Spring Boot 3 requires adjustments due to the framework's migration from the `javax` namespace to `jakarta`. Here's a concise guide to help you set up JAX-WS in your Spring Boot 3 application:

1. **Add Dependencies**:
   Include the necessary JAX-WS and JAXB dependencies in your `pom.xml`. Ensure you're using versions compatible with the `jakarta` namespace. For instance, you can use the Apache CXF starter for JAX-WS:

   ```xml
   <dependency>
       <groupId>org.apache.cxf</groupId>
       <artifactId>cxf-spring-boot-starter-jaxws</artifactId>
       <version>3.6.3</version>
   </dependency>
   ```

   Note: As of March 13, 2024, version 3.6.3 is available. Check for newer versions to ensure compatibility. ([Maven Repository](https://mvnrepository.com/artifact/org.apache.cxf/cxf-spring-boot-starter-jaxws/3.6.3?utm_source=chatgpt.com))

2. **Configure the JAX-WS Plugin**:
   If you're generating Java classes from WSDL files, configure the `jaxws-maven-plugin` in your `pom.xml`. Ensure that the plugin and its dependencies are aligned with the `jakarta` namespace:

   ```xml
   <plugin>
       <groupId>com.sun.xml.ws</groupId>
       <artifactId>jaxws-maven-plugin</artifactId>
       <version>4.0.0</version>
       <dependencies>
           <dependency>
               <groupId>jakarta.xml.ws</groupId>
               <artifactId>jakarta.xml.ws-api</artifactId>
               <version>4.0.0</version>
           </dependency>
           <dependency>
               <groupId>jakarta.xml.bind</groupId>
               <artifactId>jakarta.xml.bind-api</artifactId>
               <version>4.0.0</version>
           </dependency>
           <dependency>
               <groupId>jakarta.activation</groupId>
               <artifactId>jakarta.activation-api</artifactId>
               <version>2.1.0</version>
           </dependency>
           <dependency>
               <groupId>com.sun.xml.ws</groupId>
               <artifactId>jaxws-rt</artifactId>
               <version>4.0.0</version>
           </dependency>
       </dependencies>
       <executions>
           <execution>
               <id>wsimport-wsdl</id>
               <goals>
                   <goal>wsimport</goal>
               </goals>
               <configuration>
                   <wsdlDirectory>${project.basedir}/src/main/resources/wsdls</wsdlDirectory>
                   <wsdlFiles>
                       <wsdlFile>YourService.wsdl</wsdlFile>
                   </wsdlFiles>
                   <bindingDirectory>${project.basedir}/src/main/resources/jaxws</bindingDirectory>
                   <bindingFiles>
                       <bindingFile>wsdl_bindings.xjb</bindingFile>
                   </bindingFiles>
                   <sourceDestDir>${project.build.directory}/generated-sources/wsimport</sourceDestDir>
               </configuration>
           </execution>
       </executions>
   </plugin>
   ```

   Ensure that your binding files (`.xjb`) are updated to use the `jakarta` namespace. For example, the `wsdl_bindings.xjb` file should have the following namespace declarations:

   ```xml
   <jaxb:bindings version="3.0" xmlns:jaxb="https://jakarta.ee/xml/ns/jaxb"
       xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/"
       xmlns:xsd="http://www.w3.org/2001/XMLSchema"
       xmlns="https://jakarta.ee/xml/ns/jaxws"
       xmlns:xjc="https://jakarta.ee/xml/ns/jaxb/xjc"
       jaxb:extensionBindingPrefixes="xjc">
       <!-- Your bindings here -->
   </jaxb:bindings>
   ```

   ([جوجل](https://sites.google.com/site/pawneecity/sprint-boot/client-soap-with-maven-spring-boot?utm_source=chatgpt.com))

3. **Update Imports**:
   Refactor your Java code to replace `javax` imports with their `jakarta` equivalents. For instance, change:

   ```java
   import javax.xml.ws.BindingProvider;
   ```

   to:

   ```java
   import jakarta.xml.ws.BindingProvider;
   ```

   This ensures compatibility with the updated namespaces in Spring Boot 3

4. **Handle JAXB Exceptions**:
   After migrating, you might encounter `IllegalAnnotationsException` errors due to changes in JAXB handling. To resolve this, ensure that your JAXB dependencies are updated to versions that use the `jakarta` namespace. For example:

   ```xml
   <dependency>
       <groupId>jakarta.xml.bind</groupId>
       <artifactId>jakarta.xml.bind-api</artifactId>
       <version>4.0.0</version>
   </dependency>
   ```

   Additionally, consider switching to alternative serialization libraries like Jackson or Gson if JAXB is not a strict requirement for your project. ([Box of Curiosities](https://boxofcuriosities.co.uk/post/migrating-spring-boot-2-to-3-throws-org-glassfish-jaxb?utm_source=chatgpt.com))

By following these steps, you can successfully integrate JAX-WS into your Spring Boot 3 application, ensuring compatibility with the latest framework changes. 
