# Need to create a Dockerfile for java based applications
# Dockerfile for Eureka

FROM openjdk:18.0.2.1-jdk
ARG JAR_SRC
ENV JAV_SRC ${JAR_SRC}
RUN mkdir -p /opt/i27/
WORKDIR /opt/i27
COPY ["${JAV_SRC}" "/opt/i27/i27-eureka.jar"]
RUN chmod 777 /opt/i27/
EXPOSE 8761
CMD ["java", "-jar", "/opt/i27/i27-eureka.jar" ]