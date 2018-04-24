Spring Boot/ Spring-Cloud-Netflix-Eureka-Server 

<-Cent OS 7.4 Spring boot Cloud Netflix Eureka Server->

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



    

