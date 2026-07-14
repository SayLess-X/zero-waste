# ---- Build stage ----
FROM eclipse-temurin:25-jdk-alpine AS build
WORKDIR /app

COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
RUN chmod +x mvnw

# Cache dependencies
RUN ./mvnw -B dependency:go-offline

COPY src src
RUN ./mvnw -B clean package -DskipTests

# ---- Runtime stage ----
FROM eclipse-temurin:25-jre-alpine
WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

# Render injects PORT at runtime; Spring reads it via server.port=${PORT:8080}
EXPOSE 8080
# TEMPORARY DEBUG LINE - remove the echo once DB_URL issue is confirmed fixed
ENTRYPOINT ["sh", "-c", "echo \"===DB_URL=[$DB_URL]===\" && echo \"===DB_USERNAME=[$DB_USERNAME]===\" && exec java -jar /app/app.jar"]