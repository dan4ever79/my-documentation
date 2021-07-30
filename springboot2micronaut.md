# Migrate from Spring Boot Application code into Micronaut



This page will describe how to have a code that is still compatible both Spring Boot and Micronaut.

But the launcher may differs in order to have a less cold start with Micronaut.



## 1.- Requirements

Have a spring boot application code source project.



## 2.- Advantages of use this way

By using this manner, we have the advantages of the 2 worlds.

| Spring framework                                             | Micronaut                  |
| ------------------------------------------------------------ | :------------------------- |
| Framework<br />Lots of tutorials / samples<br />Re-use of existing code | Cold start<br />Image size |



## 3.- Howto

It'll resume into 2 main actions 

- modify the main class in order to say that application is Micronaut instead of SpringBootApplication
- modify the POM.xml in order to convert Spring annotations into a compliant Micronaut java code source.

### 3.1.- Howto - Modify the main class

- remove the `@SpringBootApplication`
- change the launcher `SpringApplication.run(...)` into `Micronaut.run(...)`

**Example with Spring Boot  - main class**

````java
package xxx.yyy.zzz;


@SpringBootApplication
@Import(
        value = {
                ServiceConfiguration.class,
        }
)
public class ServiceTemplateWsApplication {

    public static void main(String[] args) {
        SpringApplication.run(ServiceTemplateWsApplication.class, args);
    }

}

````
**Example with Micronaut  - main class**

````java
package xxx.yyy.zzz;

@Import(
        value = {
                ServiceConfiguration.class,
        }
)
public class ServiceTemplateWsApplication {

    public static void main(String[] args) {
        Micronaut.run(ServiceTemplateWsApplication.class, args);
    }

}

````



### 3.2.- Howto - Modify the POM.xml 

The longest part (1min) is separated in 3 actions :

- set start-class as properties (if not set, it's a recommendation)

- add Micronaut dependencies

- modify the build part in order to convert `Spring annotations` into a compliant Micronaut java code.

#### 3.2.1.- set start-main-class as properties (*optional*)

````xml
...
<properties>
    ...
    <start-class>xxx.yyy.zzz.ServiceTemplateWsApplication</start-class>
    ...
</properties>
...
````



#### 3.2.2.- add Micronaut dependencies

````xml
...
<properties>
    ...
    <micronaut.version>2.5.11</micronaut.version>
    <micronaut.spring.version>2.0.1</micronaut.spring.version>
    ...
</properties>
...
<dependenciesManagement>
    <dependencies>
        ...
            <dependency>
                <groupId>io.micronaut</groupId>
                <artifactId>micronaut-bom</artifactId>
                <version>${micronaut.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        ...
    </dependencies>
</dependenciesManagement>
...
<dependencies>
...
    <dependency>
        <groupId>io.micronaut</groupId>
        <artifactId>micronaut-inject</artifactId>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>ognl</groupId>
        <artifactId>ognl</artifactId>
        <version>3.1.12</version>
    </dependency>
    <dependency>
        <groupId>io.micronaut</groupId>
        <artifactId>micronaut-validation</artifactId>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>io.micronaut</groupId>
        <artifactId>micronaut-runtime</artifactId>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>io.micronaut</groupId>
        <artifactId>micronaut-http-server-netty</artifactId>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>io.micronaut</groupId>
        <artifactId>micronaut-http-client</artifactId>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>io.micronaut</groupId>
        <artifactId>micronaut-inject-java</artifactId>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>io.micronaut.views</groupId>
        <artifactId>micronaut-views-thymeleaf</artifactId>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>io.micronaut.spring</groupId>
        <artifactId>micronaut-spring-web</artifactId>
        <scope>runtime</scope>
        <version>${micronaut.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>io.micronaut.spring</groupId>
        <artifactId>micronaut-spring-context</artifactId>
        <scope>runtime</scope>
        <version>${micronaut.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <scope>runtime</scope>
    </dependency>
...
</dependencies>
````



#### 3.2.3.- modify the build part in order to convert `Spring annotations` into a compliant Micronaut java code.

````xml
...
<build>
    ...
    <plugins>
        ...
		<plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>${java.version}</source>
                <target>${java.version}</target>
                <encoding>UTF-8</encoding>
                <compilerArgs>
                    <arg>-parameters</arg>
                </compilerArgs>
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                        <version>${lombok.version}</version>
                    </path>
                    <path>
                        <groupId>io.micronaut</groupId>
                        <artifactId>micronaut-inject-java</artifactId>
                        <version>${micronaut.version}</version>
                    </path>
                    <path>
                        <groupId>io.micronaut</groupId>
                        <artifactId>micronaut-validation</artifactId>
                        <version>${micronaut.version}</version>
                    </path>
                    <path>
                        <groupId>io.micronaut.configuration</groupId>
                        <artifactId>micronaut-openapi</artifactId>
                        <version>1.2.0</version>
                    </path>
                    <path>
                        <groupId>io.micronaut.spring</groupId>
                        <artifactId>micronaut-spring-boot-annotation</artifactId>
                        <version>${micronaut.spring.version}</version>
                    </path>
                    <path>
                        <groupId>io.micronaut.spring</groupId>
                        <artifactId>micronaut-spring-web-annotation</artifactId>
                        <version>${micronaut.spring.version}</version>
                    </path>
                    <path>
                        <groupId>io.micronaut.spring</groupId>
                        <artifactId>micronaut-spring-annotation</artifactId>
                        <version>${micronaut.spring.version}</version>
                    </path>
                    <path>
                        <groupId>io.micronaut.data</groupId>
                        <artifactId>micronaut-data-processor</artifactId>
                        <version>2.4.7</version>
                    </path>
                </annotationProcessorPaths>
            </configuration>
            <executions>
                <execution>
                    <id>test-compile</id>
                    <goals>
                        <goal>testCompile</goal>
                    </goals>
                    <configuration>
                        <compilerArgs>
                            <arg>-parameters</arg>
                        </compilerArgs>
                        <annotationProcessorPaths>
                            <path>
                                <groupId>io.micronaut</groupId>
                                <artifactId>micronaut-inject-java</artifactId>
                                <version>${micronaut.version}</version>
                            </path>
                            <path>
                                <groupId>io.micronaut</groupId>
                                <artifactId>micronaut-validation</artifactId>
                                <version>${micronaut.version}</version>
                            </path>
                            <path>
                                <groupId>io.micronaut.spring</groupId>
                                <artifactId>micronaut-spring-boot-annotation</artifactId>
                                <version>${micronaut.spring.version}</version>
                            </path>
                            <path>
                                <groupId>io.micronaut.spring</groupId>
                                <artifactId>micronaut-spring-web-annotation</artifactId>
                                <version>${micronaut.spring.version}</version>
                            </path>
                        </annotationProcessorPaths>
                    </configuration>
                </execution>
            </executions>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.1.0</version>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                            <transformer
                                         implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                <mainClass>${start-class}</mainClass>
                            </transformer>
                            <transformer
                                         implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
                        </transformers>
                    </configuration>
                </execution>
            </executions>
        </plugin>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.6.0</version>
            <configuration>
                <executable>java</executable>
                <arguments>
                    <argument>-classpath</argument>
                    <classpath/>
                    <argument>${start-class}</argument>
                </arguments>
            </configuration>
        </plugin>
        <plugin>
            <groupId>com.rodiontsev.maven.plugins</groupId>
            <artifactId>build-info-maven-plugin</artifactId>
            <version>1.3</version>
            <configuration>
                <filename>classes/META-INF/build-info.properties</filename>
                <projectProperties>
                    <projectProperty>project.groupId</projectProperty>
                    <projectProperty>project.artifactId</projectProperty>
                    <projectProperty>project.version</projectProperty>
                </projectProperties>
            </configuration>
            <executions>
                <execution>
                    <phase>prepare-package</phase>
                    <goals>
                        <goal>extract</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>    
    </plugins>
    ...
</build>
...

````

# 4.- Let's go

Run the application

````bash
$> java -jar foo.jar
````



Before with Spring boot

````
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.5.0)

2021-07-30 09:37:15.341  INFO 15424 --- [           main] o.d.binary.ServiceTemplateWsApplication  : Starting ServiceTemplateWsApplication
...
2021-07-30 09:37:27.312  INFO 15424 --- [)-172.29.104.98] o.s.web.servlet.DispatcherServlet        : Completed initialization in 6 ms
````



Now with Micronaut

````
 __  __ _                                  _   
|  \/  (_) ___ _ __ ___  _ __   __ _ _   _| |_ 
| |\/| | |/ __| '__/ _ \| '_ \ / _` | | | | __|
| |  | | | (__| | | (_) | | | | (_| | |_| | |_ 
|_|  |_|_|\___|_|  \___/|_| |_|\__,_|\__,_|\__|
  Micronaut (v2.5.11)

09:38:50.008 [main] DEBUG io.micronaut.context.env.DefaultEnvironment
...
09:38:59.516 [default-nioEventLoopGroup-1-2] DEBUG io.micronaut.context.DefaultBeanContext
````

