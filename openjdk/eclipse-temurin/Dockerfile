FROM eclipse-temurin:8u482-b08-jdk-noble

# Base system setup
RUN apt update && \
    apt install -y \
        software-properties-common \
        ca-certificates \
        curl \
        gnupg \
        iputils-ping \
        net-tools \
        vim \
        locales \
        wget && \
    rm -rf /var/lib/apt/lists/*

# locale
RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG=en_US.UTF-8

# Set timezone
ENV TIMEZONE=Asia/Shanghai
ENV TZ=Asia/Shanghai
RUN set -ex && \
  echo ${TIMEZONE} > /etc/timezone && \
  ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && \
  echo ${TIMEZONE} > /etc/TZ