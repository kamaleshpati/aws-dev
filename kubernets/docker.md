## NGINX 
## docker
FROM nginx
COPY default.conf /etc/nginx/conf.d

## conf.d
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    #location / {
    #   root   /usr/share/nginx/html;
    #    index  index.html index.htm;
    #}

    location / {
    # Update your backend application Kubernetes Cluster-IP Service name  and port below
    # proxy_pass http://<Backend-ClusterIp-Service-Name>:<Port>;  
    proxy_pass http://my-backend-service:8080;
    }

    #error_page  404              /404.html;
    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    

}



## frontend

## docker
FROM nginx
COPY index.html /usr/share/nginx/html

## index.html

<!DOCTYPE html>
<html>
   <body style="background-color:lightgoldenrodyellow;">
      <h1>Welcome to Stack Simplify</h1>
      <p>Kubernetes Fundamentals Demo</p>
      <p>Application Version: V1</p>
   </body>
</html>



## backend

## docker file
FROM openjdk:8-jdk-alpine
VOLUME /tmp
EXPOSE 8080
ADD target/*.jar app.jar
ENTRYPOINT [ "sh", "-c", "java -jar /app.jar" ]

## pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.2.7.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.stacksimplify</groupId>
	<artifactId>helloworld</artifactId>
	<version>1.0.0</version>
	<name>kube-helloworld</name>
	<description>HelloWorld Spring Boot Project</description>

	<properties>
		<java.version>1.8</java.version>
		<maven.test.skip>true</maven.test.skip>
		<jar>${project.build.directory}/${project.build.finalName}.jar</jar>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
	</dependencies>

	<build>
		<finalName>hello-world-rest-api</finalName>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			<!-- Docker -->
			<plugin>
				<groupId>com.spotify</groupId>
				<artifactId>dockerfile-maven-plugin</artifactId>
				<version>1.4.6</version>
				<executions>
					<execution>
						<id>default</id>
						<goals>
							<goal>build</goal>
							<!-- <goal>push</goal> -->
						</goals>
					</execution>
				</executions>
				<configuration>
					<repository>stacksimplify/${project.name}</repository>
					<tag>${project.version}</tag>
					<skipDockerInfo>true</skipDockerInfo>
				</configuration>
			</plugin>

		</plugins>

	</build>

</project>

## code 
package com.stacksimplify.helloworld;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HelloworldApplication {

	public static void main(String[] args) {
		SpringApplication.run(HelloworldApplication.class, args);
	}

}

package com.stacksimplify.helloworld;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import com.stacksimplify.helloworld.serverinfo.ServerInformationService;

@RestController
public class HelloWorldController {
	
	@Autowired
	private ServerInformationService serverInfo;
	
	@GetMapping(path = "/hello")
	public String helloWorld() {
		return "Hello World " + " V1 " + serverInfo.getServerInfo();
	}

}

package com.stacksimplify.helloworld.serverinfo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

@Service
public class ServerInformationService {
	
	private static final String HOST_NAME = "HOSTNAME";

	private static final String INSTANCE_GUID = "LOCAL";

	@Value("${" + HOST_NAME + ":" + INSTANCE_GUID + "}")
	private String hostName;

	public String getServerInfo() {
		return hostName.substring(hostName.length()-5);
	}

}


package com.stacksimplify.helloworld;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class HelloworldApplicationTests {

	@Test
	void contextLoads() {
	}

}

