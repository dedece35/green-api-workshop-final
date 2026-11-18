#FROM maven:3.8.3-openjdk-17 AS build
# => start pb on MacOS (+ Rancher Desktop) with next message
# baseline-1  | org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'processorMetrics' defined in class path resource [org/springframework/boot/actuate/autoconfigure/metrics/SystemMetricsAutoConfiguration.class]: Failed to instantiate [io.micrometer.core.instrument.binder.system.ProcessorMetrics]: Factory method 'processorMetrics' threw exception with message: Cannot invoke "jdk.internal.platform.CgroupInfo.getMountPoint()" because "anyController" is null
# CAUSE : Votre image embarque OpenJDK 17.0.1 (octobre 2021). C’est précisément la version touchée par le bug cgroup v2 (CgroupInfo.getMountPoint() sur un contrôleur null). Il a été corrigé dans les mises à jour 17.0.x suivantes. Votre VM Rancher Desktop utilise cgroup v2, d’où le crash.
FROM maven:3-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
# Cache Maven dependencies
RUN mvn dependency:go-offline -q || true
COPY src ./src
RUN mvn -q package -DskipTests

# FROM maven:3.8.3-openjdk-17
FROM maven:3-eclipse-temurin-17
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8081
ENTRYPOINT ["java", "-XX:+UseContainerSupport", "-XX:MaxRAMPercentage=75.0", "-XX:+ExitOnOutOfMemoryError", "-Xms256m", "-Xmx1536m", "-jar", "app.jar"]

