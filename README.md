# TP Jenkins - CI/CD Insertion Service

## Description

Ce projet met en place un pipeline CI/CD avec **Jenkins** pour le microservice **diplomas-service** développé avec Spring Boot. Le `Jenkinsfile` définit les étapes du pipeline : compilation, tests, création et push de l'image Docker vers Docker Hub.

---

## Prérequis

- Un compte [GitHub](https://github.com)
- Un compte [Docker Hub](https://hub.docker.com)
- Jenkins installé
- Git

---

## Contenu du Jenkinsfile

Le pipeline Jenkins est composé de 5 stages :

### Stage 1 : Checkout
Récupère le code source depuis le repo GitHub.

### Stage 2 : Build
Compile le projet Spring Boot avec Maven :
```bash
mvn clean package -DskipTests
```

### Stage 3 : Test
Lance les tests unitaires :
```bash
mvn test
```

### Stage 4 : Build Docker Image
Construit l'image Docker du microservice :
```bash
docker build -t $DOCKER_HUB_USER/insertion-service:latest .
```

### Stage 5 : Push Docker Image
Pousse l'image vers Docker Hub :
```bash
docker push $DOCKER_HUB_USER/insertion-service:latest
```

---

## Dockerfile

Le projet utilise un **multi-stage Dockerfile** pour optimiser la taille de l'image :

```dockerfile
FROM maven:3.9.6-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B
COPY src ./src
RUN mvn clean package -DskipTests -B

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8082
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**Explication du multi-stage :**
- **Stage 1** : compile le projet avec Maven
- **Stage 2** : crée une image légère avec uniquement le JAR compilé

---

## Configuration des Variables Jenkins

Les variables sensibles sont à configurer dans Jenkins :

| Variable | Description |
|----------|-------------|
| `DOCKER_HUB_USER` | Username Docker Hub |
| `DOCKER_HUB_TOKEN` | Token/Password Docker Hub |

---

## Captures d'écran

### Pipeline Jenkins
<!-- capture pipeline Jenkins -->

### Image Docker sur Docker Hub
<!-- capture docker hub -->

---

## Auteur

**Ndeye Mbaye** — M2 Génie Logiciel
