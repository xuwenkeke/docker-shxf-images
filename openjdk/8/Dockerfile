FROM openjdk:8-jdk-stretch

LABEL MAINTAINER="张雄<zhangxiong@wondersgroup.com>"

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

ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-Duser.timezone=Asia/Shanghai", "-Dfile.encoding=UTF-8", "-jar", "/app/bootstrap.jar"]

# docker build -t shxf/openjdk:8-jdk-alpine .


# docker run -it --rm \
#   -p 7761:7761 \
#   -v /logs:/logs \
#   -v /app:/app \
#   shxf/openjdk:8-jdk-alpine
