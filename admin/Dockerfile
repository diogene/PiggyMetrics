FROM java:8-jre
MAINTAINER MOULRON Diogène

ADD ./target/admin.jar /app/
CMD ["java", "-Xmx200m", "-jar", "/app/admin.jar"]

HEALTHCHECK --interval=30s --timeout=30s CMD curl -f http://localhost:8888/actuator/health || exit 1

EXPOSE 7777