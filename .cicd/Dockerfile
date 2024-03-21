# Need to create a Dockerfile for java based applications
# Dockerfile for Eureka

FROM openjdk:18.0.2.1-jdk
RUN mkdir -p /opt/i27/
WORKDIR /opt/i27
COPY ["/home/raksharoshni/jenkins/workspace/1project_master/target/i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING}",  "/opt/i27/i27cart-eureka.jar"] 
RUN chmod 777 /opt/i27/
EXPOSE 8761
CMD ["java", "-jar", "/opt/i27/i27cart-eureka.jar"]
