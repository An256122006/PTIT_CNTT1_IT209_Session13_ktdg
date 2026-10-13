

FROM eclipse-temurin:21-jdk-jammy AS build
WORKDIR /app


COPY gradlew settings.gradle build.gradle ./
COPY gradle gradle


RUN sed -i 's/\r$//' gradlew && chmod +x gradlew


RUN ./gradlew dependencies --no-daemon || true


COPY src src
RUN ./gradlew bootJar --no-daemon -x test \
    && ls -la build/libs/ \
    && JAR="$(ls build/libs/*.jar | grep -v -- '-plain\.jar' | head -n 1)" \
    && echo "Built jar: $JAR" \
    && cp "$JAR" /app/app.jar


FROM eclipse-temurin:21-jre-jammy AS runtime
WORKDIR /app


RUN apt-get update && apt-get install -y --no-install-recommends curl \
    && rm -rf /var/lib/apt/lists/*

COPY --from=build /app/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
