# Micre Service Architecture

Spring-Cloud-Netflix-Eureka-Server 
===============================================

> **Note:**
> - Spring Cloud는 분산시스템(ex. MSA)을 구축할 때 공통적으로 발생하는 대표적인 문제에 대한 솔루션을 제공한다. 
> - 전 세계에서 MSA를 가장 잘 운영하는 기업으로 평가받는 Netflix에서는 MSA구축을 편하게 할 많은 기술과 다양한 이슈에 대한 해결책을 제공한다. 특히 Netflix OSS(Open Source Software)를 공개하고 있다. 여기에는 MSA를 구성하는데 필요한 다양한 Component들이 포함되어 있다. 
> - 결론적으로 MSA구축에 필요한 라이브러리 집합인 Netflix OSS를 Spring-Cloud 프로젝트에서 사용할 수 있다. 
> - 여기서는 Netflix OSS Component중 MSA구축에 필수적인 Eureka, Hystrix, Zuul, Ribbon 중 Eureka에 대해 알아보고 Eureka Server를 구성하여 본다.    

## Netflix Eureka - Service Discovery ##

 Eureka는 Netflix의 서비스 검색 Server및 Client이다. 각 Eureka Client가 자신의 정보를 Eureka Server에 보내고, Eureka Server는 각 EurekaClient에게 업데이된 정보를전달해주는 체계를 가진다. 이는 다수의 서비스가 지속적으로가능하게 하며, 각 Eureka Client는 Eureka Server로 부터 받은정보를 일정 시간동안 보유하고 있어 다른 서비스의 연결에 문제가 되지 않는다.

## Spring Cloud Eureka Server ##

> **Development Environment**
> - Cent OS 7.4
> - Jdk 1.8 

1. Spring-Cloud-Eureka-Server

    a. Project Folder 생성 

    b. Project Folder 하위에 Maven Project를 생성
       -  $mkdir -p src/main/java/hello

    c. pom.xml 생성
       -  vi pom.xl (원하는 Editor 사용)

--> pom.xml에 추가

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.1.RELEASE</version>
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
                <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
                <java.version>1.8</java.version>
                <spring-cloud.version>Finchley.M9</spring-cloud.version>
        <docker.image.prefix>{your_docker_image_name}</docker.image.prefix>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka-server</artifactId>
            <version>1.1.5.RELEASE</version>
        </dependency>
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
            </plugin>
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>dockerfile-maven-plugin</artifactId>
                <version>1.3.6</version>
                <configuration>
                    <repository>${docker.image.prefix}/${project.artifactId}</repository>
                <buildArgs>
                        <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
                </buildArgs>
            </configuration>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>


-->
    d. maven wrapper 설정
       - $mvn -N io.takari:maven:wrapper -Dmaven=3.3.3

    e. src/main/java/hello에 EurekaServerApplication.java 파일 생성

        EurekaServerApplication.java
        --> 
        @SpringBootApplication
        @EnableEurekaServer
        public class EurekaServerApplication {
            public static void main(String[] args) {
                SpringApplication.run(EurekaServerApplication.class, args);
            }
        }
    
    f. application.yml 파일 생성

        - $vi src/main/resources/application.yml
        - Spring Boot에서는 SnakeYAML을 포함하고 있기에 쉽게 외부파일은 YAML로 작성하여 쉽게 로드 가능.

        spring:
            application:
                name: eureka-server
        server:
            port: 8761

        eureka:
            client:
                registerWithEureka: false
                fetchRegistry: false

    
    g. maven wrapper build

        - Project Folder에서 package build
        - $./mvnw package

    h. jar 파일 실행

        - $java -jar target/spring-boot-docker-sm1-0.1.0.jar



    

