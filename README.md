# feature-toggles-demo

Demo repository for testing features toggles using unleash with a java sdk

# Using Feature Toggles with Unleash Tool - Step-by-Step Guide

This repository contains step-by-step instructions for using feature toggles with the Unleash tool through an SDK in
Java. It also includes a Java application called `movies-app` that utilizes the Unleash SDK.

## Prerequisites

Before you begin, make sure you have the following:

- Java 17 installed and Java 8
- Maven installed
- Docker installed

## FeatureToggleSdk Repository

The `FeatureToggleSdk` repository contains an SDK developed in Java 8 that provides classes and configurations for using
Unleash with toggle names, tenants, companies, or custom fields. To set up the `FeatureToggleSdk` repository, follow
these steps:

1. Clone the repository from the following
   link: [FeatureToggleSdk repository](https://github.com/fernando-dm/FeatureToggleSdk).
2. Open a terminal or command prompt and navigate to the cloned repository's directory.
3. Build and publish the SDK to the local Maven repository by running the following command:

**Don't forget to set java17 for running app-movies and set java8 for build sdk**

If you have [sdk man](https://sdkman.io/):
```shell
sdk install java 8.0.382-amzn # optional step
sdk use java 8.0.382-amzn
```

```shell
gradle wrapper
./gradlew clean build publishToMavenLocal --info
```

This command will create a JAR file in the local `.m2` repository.


## Repository: movies-app

The `movies-app` repository contains a Java application developed with Java 17, JDBC Template, FlywayDB, and classes for
configuring Unleash. It also uses an SDK specifically created for this project. To set up the `movies-app` repository,
follow these steps:

1. Clone the repository from the following link: [movies-app repository](https://github.com/fernando-dm/movies-app).
2. Open a terminal or command prompt and navigate to the cloned repository's directory.
3. Build the Maven wrapper by running the following command:

```shell
mvn -N wrapper:wrapper
```

5. Build and package the application by running the following command:

```shell
./mvnw clean package
```

## Configure Unleash

To configure Unleash and create the necessary toggle names and custom fields, follow these steps:

1. Start the Unleash server using Docker Compose. Create a `docker-compose.yml` file with the following content:

```yaml
version: '3.4'
services:
  postgres-unleash:
    image: postgres:13
    container_name: postgres-unleash
    environment:
      - POSTGRES_USER=unleash_user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=unleash
    volumes:
      - unleash:/data/postgres
    networks:
      - unleash
    restart: unless-stopped

  unleash-server:
    image: unleashorg/unleash-server:latest
    container_name: unleash-server
    environment:
      - DATABASE_HOST=postgres-unleash
      - DATABASE_NAME=unleash
      - DATABASE_USERNAME=unleash_user
      - DATABASE_PASSWORD=password
      - LOG_LEVEL=INFO
      - DATABASE_SSL=false
    depends_on:
      - postgres-unleash
    networks:
      - unleash
    ports:
      - "4242:4242"
    tty: false

networks:
  unleash:
    driver: bridge

volumes:
  unleash:
```

1. Open a terminal or command prompt and navigate to the directory containing the docker-compose.yml file.
2. Start the Unleash server by running the following command:

   ```docker-compose up -d```

3. start movies-db:

   ```shell docker compose up postgres```
4. start movies-app through an ide (and get fun with breakpoints (: ) or simply by run:

**dont forget to set java17 for running app-movies and set java8 for build sdk**

```shell
If you have sdk man:

sdk use java 17.0.1.12.1-amzn
```
```shell
./mvnw clean package spring-boot:run
```

## Test the Application
Once Unleash is set up and configured, you can test the movies-app application using the following curl commands:

. By Premium:

```shell
curl http://localhost:8080/api/v1/movies/premium/tenant/2_cafe | jq
curl http://localhost:8080/api/v1/movies/premium/tenant/2_cafe/company/1_sucursal_devoto | jq
```

. Custom:

```shell
curl http://localhost:8080/api/v1/movies/premium/custom/tenant/2_cafe | jq
curl http://localhost:8080/api/v1/movies/premium/custom/tenant/2_cafe/company/1_sucursal_devoto | jq
```

## Deployment

To deploy the movies-app application with the SDK, follow these steps:

1. Navigate to the movies-app repository's root folder.
2. Execute the deploy.sh script. Note that you need to be in the folder containing the script.

```shell
./deploy.sh
```

This script uses the previously created SDK and copies the JAR file into the Docker image.

3. After running the script, execute the following command in the root folder of the repository to start the application
   and its dependencies in Docker:

```shell
docker-compose up -d
```

## Communication between Unleash and movies-app

WIP: Describe the communication process between the Unleash Docker container and the movies-app application.

That's it! You've successfully set up and configured feature toggles with the Unleash tool using the provided SDK and
the movies-app application.

Enjoy experimenting with feature flags and managing feature releases in your application.

