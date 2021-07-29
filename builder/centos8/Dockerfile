FROM centos:centos8.3.2011

ENV LANG=en_US.UTF-8

# Set timezone
ENV TIMEZONE=Asia/Shanghai
RUN set -ex \
  && echo ${TIMEZONE} > /etc/timezone \
  && ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
  && echo ${TIMEZONE} > /etc/TZ

# Change yum repositry
RUN set -ex \
  && ls -lh /etc/yum.repos.d \
  && mv /etc/yum.repos.d/CentOS-Linux-BaseOS.repo /etc/yum.repos.d/CentOS-Linux-BaseOS.repo.backup \
  && curl -o /etc/yum.repos.d/CentOS-Linux-BaseOS.repo https://mirrors.aliyun.com/repo/Centos-8.repo \
  && yum clean all && yum makecache \
  && yum install -y git wget \
  && yum clean all \
  && rm -rf /var/cache/yum/*

# Install JDK
ARG JDK_TAR=/files/jdk-8u301-linux-aarch64.tar
COPY ${JDK_TAR} /usr/local/java/jdk.tar.gz
RUN set -ex \
  && tar -zxf /usr/local/java/jdk.tar.gz -C /usr/local/java/ \
  && cd /usr/local/java \
  && ls -lh \
  && ln -s /usr/local/java/jdk1.8.0_301 default \
  && sed -i 's/securerandom.source=file:\/dev\/random/securerandom.source=file:\/dev\/.\/urandom/g' default/jre/lib/security/java.security \
  && rm /usr/local/java/jdk.tar.gz

ENV JAVA_HOME=/usr/local/java/default
ENV JAVA_BIN=${JAVA_HOME}/bin
ENV PATH=$PATH:$JAVA_BIN
ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# Install Maven
ARG MAVEN_VERSION=3.6.3
RUN set -ex \
  && mkdir -vp /usr/local/maven \
  && curl -o /usr/local/maven/maven.tar.gz https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz \
  && cd /usr/local/maven/ && tar -zxf /usr/local/maven/maven.tar.gz \
  && ln -s /usr/local/maven/apache-maven-${MAVEN_VERSION} default \
  && ln -s /usr/local/maven/default/bin/mvn /usr/bin/mvn \
  && rm /usr/local/maven/maven.tar.gz

ENV MAVEN_HOME=/usr/local/maven/default