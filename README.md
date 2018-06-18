
Spring-Cloud-Netflix-Eureka-Server 
===============================================

*by S.M Lee*

> **Note:**
>
> - 전 세계에서 MSA를 가장 잘 운영하는 기업으로 평가받는 Netflix에서는 MSA구축을 편하게 할 많은 기술과 다양한 이슈에 대한 해결책을 제공한다. 특히 Netflix OSS(Open Source Software)를 공개하고 있다. 여기에는 MSA를 구성하는데 필수적으로 고려해야 할 다양한 Component들이 포함되어 있다. 
>
> - 또한 Spring Cloud는 분산시스템(ex. Microservice Architecture)을 구축할 때 공통적으로 발생하는 대표적인 문제에 대한 솔루션을 제공한다. 
> - 그리고 Spring Cloud에서는 위에서 설명한 Netflix OSS를 사용할 수 있게 Spring Cloud Netflix를 제공하고 있다.
>
> - 결론적으로 MSA구축에 필요한 라이브러리 집합인 Netflix OSS를 Spring Cloud 프로젝트에서 사용할 수 있다. 
>
> - 여기서는 Netflix OSS Component중 MSA구축에 필수적인 Eureka-Service Discovery & Registry, Hystrix-Circuit Breaker, Zuul-API Gateway, Ribbon-Client Side Loadbalancer 중 Eureka에 대해 알아본다.   

## Netflix Eureka - Service Discovery & Registry##
 
 * Eureka란 말 그대로 Service Discovery & Registry 이다. Microservice Architecture에서는 기존의 Legacy한 Monolithic Architecture와는 달리 작은 Service 단위로 시스템이 구축 된다.
 
 * Service 단위로 시스템이 구축되다 보니 MSA에서는 이 Service들은 어떠한 Registry에 등록하고 이 Registry를 기반으로 다른 서비스를 찾는 Service Discovery & Registry라는 개념이 필요하다.
 
 * Netflix에서는 Eureka라는 Service Discovery & Registry를 제공하고 있고, 우리는 잘 파악해서 사용 하면 된다.
 
 * 그래서 Eureka는 어떻게 이루어져 있을까?
  
 * Eureka는 Eureka Server와 Eureka Client로 나뉘어 지는데, MSA에서의 모든 service들을 Eureka Client로 만들고, 이 Eureka Client들을 Eureka Server에 등록하게 된다. Eureka Client가 자신을 Eureka Server에 등록하면 hostname, ip address, port 등 meta data를 Eureka Server에 전송한다.
 
이 meta data는 Eureka Server(Registry)에 저장되는 것이다. 그리고 등록된 Eureka Client는 이 Registry에 저장된 다른 Eureka Client의 정보를 사용할 수 있다. 이 부분은 아마 계속 진행하다보면 이해가 될 것이다.

그리고 Eureka Server는 각 Client로 부터 30초마다(default) heartbeat(Eureka Client가 살아있음을 알리는)를 수신하게 된다. Client로 부터 heartbeat가 오지 않으면 Eureka Server는 이 Client가 죽은 것으로 판단하고 Registry에서 제거하게 된다.


 
 * 각 Eureka Client가 자신의 정보를 Eureka Server에 보내고, Eureka Server는 각 Eureka Client에게 업데이트 된 정보를 전달해주는 체계를 가진다. 
 이는 다수의 서비스가 지속적으로가능하게 하며, 각 Eureka Client는 Eureka Server로 부터 받은정보를 일정 시간동안 보유하고 있어 다른 서비스의 연결에 문제가 되지 않는다.


## Spring Cloud Eureka Server ##


> **Development Environment**
> - Cent OS 7.4
> - Jdk 1.8 
> - Maven wrapper-Dmaven=3.3.3




**1. Set Maven Project Folder**

>a. Project Folder 생성 
>
>
>b. Project Folder 하위에 Maven Project를 생성
> -     $mkdir -p src/main/java/hello
>
>
>


**2. Pom.xml**

>a. pom.xml 생성
>
> -     $vi pom.xl (원하는 Editor 사용)
>

 **pom.xml**

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





**3. Maven Wrapper**

>
>a. maven wrapper 설정
>
> -     $mvn -N io.takari:maven:wrapper -Dmaven=3.3.3
>  
>
>
      
**4. EurekaServerApplication.java**

>a. src/main/java/hello에 EurekaServerApplication.java 파일 생성
>



   **EurekaServerApplication.java**

        @SpringBootApplication
        @EnableEurekaServer
        public class EurekaServerApplication {
            public static void main(String[] args) {
                SpringApplication.run(EurekaServerApplication.class, args);
            }
        }
        



**5. Application.yml**

>a. application.yml 파일 생성
>
>
> - **Tip**
> - Spring Boot에서는 SnakeYAML을 포함하고 있기에 외부파일은 YAML로 작성하여 쉽게 로드 가능


> -     $vi src/main/resources/application.yml



**application.yml**

         spring:
             application:
                 name: eureka-server
         server:
             port: 8761

         eureka:
             client:
                 registerWithEureka: false
                 fetchRegistry: false



**6. Maven Package**

>
>a. maven wrapper build
>
> -     $cd {YourProjectFolder}
> -     $./mvnw package
>
>


**7. Execute jar**

>
>a. jar 파일 실행
>
> -     $java -jar target/spring-boot-docker-sm1-0.1.0.jar
>
>

**8. Make Docker images**
>
> -     $./mvnw dockerfile:build
> -     $docker images
>
>

**9. Run Docker Container**
>
> -     $docker run -it 8761:8761 {your_container_id}
> -     $docker ps 
>
>

**10. Check your Eureka Dashboard**

> http://localhost:8761

![eureka](https://user-images.githubusercontent.com/20153890/39235281-755c1428-48b0-11e8-807a-c33bb67f7fd1.PNG)


