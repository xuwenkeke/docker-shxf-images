FROM tomcat:8.5.70-jdk8-openjdk-buster

# Set timezone
ENV LANG en_US.UTF-8
ENV TIMEZONE Asia/Shanghai
RUN set -ex \
  && echo ${TIMEZONE} > /etc/timezone \
  && ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
  && echo ${TIMEZONE} > /etc/TZ

# Install 
RUN set -ex \
  && apt-get update && apt install net-tools \
  && apt remove openjdk-8

# Install JDK
ARG JAVA_VERSION=jdk-8u301
RUN set -ex \
  && mkdir -vp /usr/local/java \
  && wget --no-cookies \
      --no-check-certificate \
      "https://www.dropbox.com/s/5v0wvp7vdy27cp8/${JAVA_VERSION}-linux-$(arch).tar.gz?dl=0" \
      -O /jdk.tar.gz \
  && tar -zxf /jdk.tar.gz -C /usr/local/java/ --strip-components=1 --no-same-owner \
  && sed -i 's/securerandom.source=file:\/dev\/random/securerandom.source=file:\/dev\/.\/urandom/g' /usr/local/java/jre/lib/security/java.security \
  && rm -v /jdk.tar.gz \
  && ls -lh /usr/local/openjdk-8

ENV JAVA_HOME=/usr/local/java
ENV JAVA_BIN=${JAVA_HOME}/bin
ENV PATH=$PATH:$JAVA_BIN
ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# Set timezone
ENV TIMEZONE Asia/Shanghai
RUN set -ex \
  && echo ${TIMEZONE} > /etc/timezone \
  && ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
  && echo ${TIMEZONE} > /etc/TZ

# Create User
ARG RUN_USER=xf
RUN set -ex \
  && addgroup --gid "800" "$RUN_USER" \
  && adduser --disabled-password --gecos "" --home "$(pwd)" --ingroup "$RUN_USER" --no-create-home --uid "800" "$RUN_USER"

USER ${RUN_USER}
WORKDIR ${CATALINA_HOME}

EXPOSE 8080/tcp
CMD ["catalina.sh", "run"]