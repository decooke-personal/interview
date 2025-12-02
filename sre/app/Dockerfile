# Stage 1: Build the application
FROM maven:3.9.11-amazoncorretto-25-al2023 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -ntp

# Stage 2: Create the final runtime image
FROM amazoncorretto:25-al2023-headless
WORKDIR /app
COPY --from=build /app/target/*.jar backend-api.jar
EXPOSE 8080
CMD ["java", "--add-opens", "java.base/java.lang=ALL-UNNAMED", "-jar", "backend-api.jar"]
