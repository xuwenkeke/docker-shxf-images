FROM ubuntu:22.04

USER root

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

# Test
RUN wget --version && \
    vim --version && \
    curl --version && \
    date

# Install JDK
ARG JAVA_VERSION=jdk-8u301
ARG TARGETPLATFORM
RUN case "${TARGETPLATFORM}" in \
        "linux/amd64") ARCH=amd64 ;; \
        "linux/arm64") ARCH=arm64 ;; \
        *) echo "Unsupported platform: ${TARGETPLATFORM}"; exit 1 ;; \
    esac; \
    mkdir -vp /usr/local/java && \
    wget --no-check-certificate --quiet \
        -O /jdk.tar.gz \
        "https://www.dropbox.com/scl/fi/6ymz8izi4ndf549gt1rcq/${JAVA_VERSION}-linux-$(ARCH).tar.gz?rlkey=x0xukjsoi8dui846a8mw8e85x&st=dk7a9tmx&dl=0" && \
      tar -zxf /jdk.tar.gz -C /usr/local/java/ --strip-components=1 --no-same-owner && \
      sed -i 's/securerandom.source=file:\/dev\/random/securerandom.source=file:\/dev\/.\/urandom/g' /usr/local/java/jre/lib/security/java.security && \
      rm -v /jdk.tar.gz

ENV JAVA_HOME=/usr/local/java
ENV JAVA_BIN=${JAVA_HOME}/bin
ENV PATH=$PATH:$JAVA_BIN
ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# Create User
ARG RUN_USER=xf
RUN groupadd -r ${RUN_USER} --gid 800 && \
  useradd -r --gid ${RUN_USER} --uid 800 --shell /bin/bash ${RUN_USER} && \
  mkdir -vp /home/${RUN_USER}/tongweb && \
  chown -R ${RUN_USER}:${RUN_USER} /home/${RUN_USER}

# change User
USER ${RUN_USER}

# Install Tongweb
WORKDIR /home/${RUN_USER}/
COPY --chown=${RUN_USER}:${RUN_USER} ./tongweb/7/install ./install
RUN wget --quiet \
      --no-check-certificate \
      -O ./install/TongWeb_Enter_Linux.tar.gz \
      "https://www.dropbox.com/s/wbdzd5x8asmkr69/TongWeb7.0.4.3_Enter_Linux.tar.gz?dl=0" && \
    tar -zxf ./install/TongWeb_Enter_Linux.tar.gz -C ./tongweb --no-same-owner

# Run Tongweb
ENV TONGWEB_HOME=/home/${RUN_USER}/tongweb
ENV TONGWEB_VERSION=7.0.4.3

WORKDIR ${TONGWEB_HOME}

EXPOSE 8088/tcp
EXPOSE 9060/tcp

CMD ["bin/startserver.sh"]


# 如果由于dockerhub编译后的镜像, 下载太慢, 本地编译
# git clone git@github.com:xuwenkeke/docker-shxf-images.git
# cd docker-shxf-images
# 开启ClashX全局代理
# docker buildx build --platform linux/amd64 -t xuwenkeke/tongweb:7 -f tongweb/7/Dockerfile .