# Use an official Maven image to build the application
FROM maven:3.9-eclipse-temurin-22-alpine AS build
WORKDIR /app

# Copy the Maven project files and install dependencies
COPY pom.xml .
RUN mvn dependency:go-offline

# Copy the source code and build the application
COPY src ./src
RUN mvn package -DskipTests

# Use a lightweight JDK image to run the application
FROM eclipse-temurin:22-jdk-alpine
WORKDIR /app

# curl is needed for the HEALTHCHECK below
RUN apk add --no-cache curl

# Copy the JAR file from the build stage
COPY --from=build /app/target/*.jar app.jar

# Expose the necessary port
EXPOSE 8081

# Tell Docker how to check if the container is actually healthy,
# not just "running". Hits Spring Boot Actuator's health endpoint.
HEALTHCHECK --interval=15s --timeout=5s --start-period=40s --retries=5 \
  CMD curl -f http://localhost:8081/actuator/health || exit 1

# Set the entry point to run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
