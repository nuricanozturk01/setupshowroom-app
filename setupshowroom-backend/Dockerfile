FROM --platform=$BUILDPLATFORM maven:3.9.6-eclipse-temurin-21-alpine AS build

ARG TARGETPLATFORM
ARG BUILDPLATFORM

WORKDIR /app

COPY .mvn .mvn
COPY mvnw .
COPY pom.xml .

COPY src ./src

RUN mvn clean package -DskipTests -B

FROM eclipse-temurin:21-jre-alpine

WORKDIR /app

LABEL org.opencontainers.image.description="setup-showroom Backend"

COPY --from=build /app/target/*.jar backend.jar

RUN apk add --no-cache curl

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
  CMD curl -f http://localhost:8080/actuator/health/liveness || exit 1

ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0 -XX:+UseG1GC"

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar backend.jar"]
