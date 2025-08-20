FROM ubuntu:jammy-20250730


# Locales
RUN apt-get update && apt-get install -y locales wget vim && rm -rf /var/lib/apt/lists/* \
	&& localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8 \
  && wget --version && vim --version \
  && apt-get clean
ENV LANG=en_US.UTF-8

# Set timezone
ENV TIMEZONE=Asia/Shanghai
RUN set -ex \
  && timedatectl | grep "Time zone" \
  && timedatectl set-timezone ${TIMEZONE} \
  && timedatectl | grep "Time zone" && cat /etc/timezone && ls -lh /etc/localtime && cat /etc/TZ

# Install JDK
ARG JAVA_VERSION=jdk-8u301
RUN set -ex \
  && mkdir -vp /usr/local/java \
  && wget --quiet --no-cookies \
      --no-check-certificate \
      --header "Cookie: oraclelicense=accept-securebackup-cookie" \
      "https://download.oracle.com/otn-pub/java/jdk/8u301-b09/d3c52aa6bfa54d3ca74e617f18309292/${JAVA_VERSION}-linux-aarch64.tar.gz" \
      -O /jdk.tar.gz \
  && tar -zxf /jdk.tar.gz -C /usr/local/java/ --strip-components=1 --no-same-owner \
  && sed -i 's/securerandom.source=file:\/dev\/random/securerandom.source=file:\/dev\/.\/urandom/g' /usr/local/java/jre/lib/security/java.security \
  && rm -v /jdk.tar.gz

ENV JAVA_HOME=/usr/local/java
ENV JAVA_BIN=${JAVA_HOME}/bin
ENV PATH=$PATH:$JAVA_BIN
ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# Create User
ARG RUN_USER=xf
RUN set -ex \
  && groupadd -r ${RUN_USER} --gid 800 \
  && useradd -r --gid ${RUN_USER} --uid 800 --shell /bin/bash ${RUN_USER} \
  && mkdir -vp /home/${RUN_USER}/tongweb \
  && chown -R ${RUN_USER}:${RUN_USER} /home/${RUN_USER}  

# change User
USER ${RUN_USER}

# Install Tongweb
WORKDIR /home/${RUN_USER}/
COPY --chown=${RUN_USER}:${RUN_USER} ./tongweb/7/install ./install
RUN set -ex \
  && wget --no-check-certificate "https://www.dropbox.com/s/wbdzd5x8asmkr69/TongWeb7.0.4.3_Enter_Linux.tar.gz?dl=0" -O ./install/TongWeb_Enter_Linux.tar.gz \
  && tar -zxf ./install/TongWeb_Enter_Linux.tar.gz -C ./tongweb --no-same-owner


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