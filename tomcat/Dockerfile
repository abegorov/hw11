FROM eclipse-temurin:17-jre-alpine
ENV TOMCAT_MAJOR 10
ENV TOMCAT_VERSION 10.1.13
RUN --mount=type=cache,target=/var/cache/apk,sharing=locked \
    --mount=type=tmpfs,target=/tmp \
    apk update && \
    wget -O /tmp/tomcat.tar.gz \
        https://dlcdn.apache.org/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz && \
    mkdir -p /opt/tomcat && \
    tar xf /tmp/tomcat.tar.gz --strip-components 1 --directory /opt/tomcat && \
    rm /opt/tomcat/webapps/* -rf
EXPOSE 8080
WORKDIR /opt/tomcat
CMD [ "/opt/tomcat/bin/catalina.sh", "run" ]
COPY --link ../puzzle15/target/Puzzle15-1.0-SNAPSHOT.war /opt/tomcat/webapps/puzzle15.war
