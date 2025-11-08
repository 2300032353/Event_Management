# Backend Dockerfile - build with Maven and run the Spring Boot WAR
FROM maven:3.8.8-eclipse-temurin-17 AS builder
WORKDIR /workspace

# copy maven config and download dependencies first
COPY pom.xml .
COPY mvnw mvnw
COPY .mvn .mvn

# copy source
COPY src ./src

RUN mvn -B package -DskipTests

FROM eclipse-temurin:17-jre
WORKDIR /app

# Copy the built WAR from the builder stage
COPY --from=builder /workspace/target/*.war app.war

EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/app.war"]
