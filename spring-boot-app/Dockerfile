# You can change this base image to anything else
# But make sure to use the correct version of Java
#FROM adoptopenjdk/openjdk11:alpine-jre

# Simply the artifact path
#ARG artifact=target/spring-boot-web#.jar
#WORKDIR /opt/app

#COPY ${artifact} app.jar
#COPY target/spring-boot-demo-1.0-SNAPSHOT.jar app.jar
# This should not be changed
#ENTRYPOINT ["java","-jar","app.jar"]
#FROM openjdk:11-slim
#WORKDIR /app

# Copy the JAR file generated from Maven
#COPY target/spring-boot-demo-1.0-SNAPSHOT.jar app.jar

# Run the application
#CMD ["java", "-jar", "app.jar"]
# Modern, actively maintained Java 11 image
FROM eclipse-temurin:11-jre-alpine

WORKDIR /opt/app

COPY target/spring-boot-demo-1.0-SNAPSHOT.jar app.jar

ENTRYPOINT ["java","-jar","app.jar"]
