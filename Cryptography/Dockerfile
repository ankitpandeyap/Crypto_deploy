# Use a slim OpenJDK base image for smaller size
FROM openjdk:17-jdk-slim as builder

# Set the working directory inside the container
WORKDIR /app

# Copy the pom.xml and download dependencies to leverage Docker layer caching
COPY pom.xml .
# Download dependencies without building the app, this layer will be cached
# unless pom.xml changes
RUN mvn dependency:go-offline -B

# Copy the rest of the application source code
COPY src ./src

# Build the Spring Boot application
# The target JAR will be in target/
RUN mvn clean install -DskipTests

# Use a JRE-only base image for the final runtime, even smaller
# --- NEW CRITICAL CHANGE ON THIS LINE ---
FROM openjdk:17-jre-slim-bullseye

# Set the working directory
WORKDIR /app

# Copy the built JAR file from the builder stage
COPY --from=builder /app/target/*.jar app.jar

# Expose the port your application listens on (Render will remap this)
EXPOSE 8082

# Command to run the application
# Spring Boot automatically picks up the PORT environment variable from Render
ENTRYPOINT ["java", "-jar", "app.jar"]
