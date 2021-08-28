FROM tomcat:8.5.70-jdk8-openjdk-buster

# Set timezone
ENV LANG en_US.UTF-8
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
