FROM eclipse-temurin:8u482-b08-jdk-noble

# Set timezone
ENV TIMEZONE=Asia/Shanghai
ENV TZ=Asia/Shanghai
RUN set -ex && \
    echo ${TIMEZONE} > /etc/timezone && \
    ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && \
    echo ${TIMEZONE} > /etc/TZ

ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-Duser.timezone=Asia/Shanghai", "-Dfile.encoding=UTF-8", "-jar", "/app/bootstrap.jar"]