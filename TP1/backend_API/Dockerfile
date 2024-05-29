# Choose a Java JRE base image
FROM openjdk:latest

# Set the working directory inside the container
WORKDIR /app

# Copy the compiled Java class file into the container
COPY Main.class /app

# Run the Java class
CMD ["java", "Main"]
