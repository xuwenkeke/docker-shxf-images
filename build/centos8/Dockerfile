FROM centos:7.5.1804

# Set timezone
ENV LANG en_US.UTF-8
ENV TIMEZONE Asia/Shanghai
RUN set -ex \
  && echo ${TIMEZONE} > /etc/timezone \
  && ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
  && echo ${TIMEZONE} > /etc/TZ
