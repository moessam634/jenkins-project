# You can change this base image to anything else
# But make sure to use the correct version of Java

# Simply the artifact path
FROM eclipse-temurin:11-jre-alpine
WORKDIR /opt/app

COPY target/spring-boot-demo-1.0-SNAPSHOT.jar app.jar
# This should not be changed
ENTRYPOINT ["java","-jar","app.jar"]


