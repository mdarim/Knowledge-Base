To generate web services in a Spring Boot 3 application using an offline WSDL file, follow these steps:

1. **Set Up the Spring Boot Project**:
    - Initialize a new Spring Boot project with the necessary dependencies. You can use the [Spring Initializr](https://start.spring.io/) to generate a project with the "Spring Web" and "Spring Web Services" starters.

2. **Add Required Dependencies**:
    - In your `pom.xml`, include the following dependencies:

      ```xml
      <dependencies>
          <!-- Spring Web Services -->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web-services</artifactId>
          </dependency>
          <!-- JAXB for XML binding -->
          <dependency>
              <groupId>jakarta.xml.bind</groupId>
              <artifactId>jakarta.xml.bind-api</artifactId>
          </dependency>
          <!-- WSDL4J for WSDL parsing -->
          <dependency>
              <groupId>wsdl4j</groupId>
              <artifactId>wsdl4j</artifactId>
          </dependency>
      </dependencies>
      ```

3. **Configure the JAXB2 Maven Plugin**:
    - To generate Java classes from your offline WSDL file, configure the `jaxb2-maven-plugin` in your `pom.xml`:

      ```xml
      <build>
          <plugins>
              <plugin>
                  <groupId>org.codehaus.mojo</groupId>
                  <artifactId>jaxb2-maven-plugin</artifactId>
                  <version>3.1.0</version>
                  <executions>
                      <execution>
                          <id>generate-sources</id>
                          <goals>
                              <goal>xjc</goal>
                          </goals>
                          <configuration>
                              <schemaDirectory>${project.basedir}/src/main/resources/wsdl</schemaDirectory>
                              <outputDirectory>${project.basedir}/src/main/java</outputDirectory>
                          </configuration>
                      </execution>
                  </executions>
              </plugin>
          </plugins>
      </build>
      ```

        - Place your WSDL file in the `src/main/resources/wsdl` directory of your project.
        - Run the Maven command `mvn clean compile` to generate the Java classes from the WSDL.

4. **Implement the Service Endpoint**:
    - Create a class to handle incoming SOAP requests. Annotate it with `@Endpoint` and define methods to process the requests:

      ```java
      package com.example.demo;
 
      import org.springframework.ws.server.endpoint.annotation.Endpoint;
      import org.springframework.ws.server.endpoint.annotation.PayloadRoot;
      import org.springframework.ws.server.endpoint.annotation.RequestPayload;
      import org.springframework.ws.server.endpoint.annotation.ResponsePayload;
 
      @Endpoint
      public class MyServiceEndpoint {
 
          private static final String NAMESPACE_URI = "http://example.com/my-service";
 
          @PayloadRoot(namespace = NAMESPACE_URI, localPart = "MyRequest")
          @ResponsePayload
          public MyResponse handleMyRequest(@RequestPayload MyRequest request) {
              // Implement your business logic here
              MyResponse response = new MyResponse();
              // Set response properties
              return response;
          }
      }
      ```

        - Replace `MyRequest` and `MyResponse` with the appropriate classes generated from your WSDL.

5. **Configure the Web Service**:
    - Set up the `MessageDispatcherServlet` and define the WSDL location:

      ```java
      package com.example.demo;
 
      import org.springframework.boot.web.servlet.ServletRegistrationBean;
      import org.springframework.context.ApplicationContext;
      import org.springframework.context.annotation.Bean;
      import org.springframework.context.annotation.Configuration;
      import org.springframework.core.io.ClassPathResource;
      import org.springframework.ws.config.annotation.EnableWs;
      import org.springframework.ws.transport.http.MessageDispatcherServlet;
      import org.springframework.ws.wsdl.wsdl11.DefaultWsdl11Definition;
      import org.springframework.xml.xsd.SimpleXsdSchema;
      import org.springframework.xml.xsd.XsdSchema;
 
      @EnableWs
      @Configuration
      public class WebServiceConfig {
 
          @Bean
          public ServletRegistrationBean<MessageDispatcherServlet> messageDispatcherServlet(ApplicationContext applicationContext) {
              MessageDispatcherServlet servlet = new MessageDispatcherServlet();
              servlet.setApplicationContext(applicationContext);
              servlet.setTransformWsdlLocations(true);
              return new ServletRegistrationBean<>(servlet, "/ws/*");
          }
 
          @Bean(name = "myService")
          public DefaultWsdl11Definition defaultWsdl11Definition(XsdSchema myServiceSchema) {
              DefaultWsdl11Definition wsdl11Definition = new DefaultWsdl11Definition();
              wsdl11Definition.setPortTypeName("MyServicePort");
              wsdl11Definition.setLocationUri("/ws");
              wsdl11Definition.setTargetNamespace("http://example.com/my-service");
              wsdl11Definition.setSchema(myServiceSchema);
              return wsdl11Definition;
          }
 
          @Bean
          public XsdSchema myServiceSchema() {
              return new SimpleXsdSchema(new ClassPathResource("wsdl/myService.xsd"));
          }
      }
      ```

        - Ensure that the `myService.xsd` file is located in the `src/main/resources/wsdl` directory.

6. **Run the Application**:
    - Start your Spring Boot application. The WSDL should be accessible at `http://localhost:8080/ws/myService.wsdl`.

By following these steps, you can generate and expose a SOAP web service in a Spring Boot 3 application using an offline WSDL file. This approach leverages Spring Web Services and JAXB for XML binding. 
