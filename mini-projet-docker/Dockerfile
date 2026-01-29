# Use a lightweight Java 17 base image
FROM amazoncorretto:17-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy the compiled Spring Boot JAR from the target folder
COPY target/paymybuddy.jar ./paymybuddy.jar

# Expose port 8080 for the backend application
EXPOSE 8080

# Command to run the application when the container starts
CMD ["java", "-jar", "paymybuddy.jar"]
