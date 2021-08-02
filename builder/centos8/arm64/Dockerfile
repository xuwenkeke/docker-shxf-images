FROM centos:centos8.3.2011

ENV LANG=en_US.UTF-8

# Change yum repositry
RUN set -ex \
  && ls -lh /etc/yum.repos.d \
  && mv /etc/yum.repos.d/CentOS-Linux-BaseOS.repo /etc/yum.repos.d/CentOS-Linux-BaseOS.repo.backup \
  && curl -o /etc/yum.repos.d/CentOS-Linux-BaseOS.repo https://mirrors.aliyun.com/repo/Centos-8.repo \
  && yum makecache \
  && yum install -y git wget glibc-locale-source glibc-langpack-en \
  && yum clean all \
  && rm -rf /var/cache/yum/*

# Set timezone
ENV TIMEZONE=Asia/Shanghai
RUN set -ex \
  && localedef -i en_US -f UTF-8 en_US.UTF-8 \
  && echo ${TIMEZONE} > /etc/timezone \
  && ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
  && echo ${TIMEZONE} > /etc/TZ
  
# Install JDK
ARG JAVA_VERSION=jdk-8u301
RUN set -ex \
  && mkdir -vp /usr/local/java \
  && wget --no-cookies \
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

# Install Maven
ARG MAVEN_VERSION=3.6.3
RUN set -ex \
  && mkdir -vp /usr/local/maven \
  && wget --no-check-certificate "https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz" -O /maven.tar.gz \
  && tar -zxf /maven.tar.gz -C /usr/local/maven --strip-components=1 --no-same-owner \
  && ln -s /usr/local/maven/bin/mvn /usr/bin/mvn \
  && rm -v /maven.tar.gz

ENV MAVEN_HOME=/usr/local/maven

# Install nodejs
# Download: https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz
ARG NODE_VERSION=10.9.0
ARG NODE_ARCH=arm64
RUN set -ex \
  && mkdir -vp /usr/local/node \
  && wget --no-check-certificate "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$NODE_ARCH.tar.gz" -O /node.tar.xz \
  && tar -zxf /node.tar.xz -C /usr/local/node --strip-components=1 --no-same-owner \
  && ln -s /usr/local/node/bin/node /usr/local/bin/node \
  && ln -s /usr/local/node/bin/npm /usr/local/bin/npm \
  && rm -v /node.tar.xz

# Install yarn
# Download: https://github.com/yarnpkg/yarn/releases/download/v1.22.11/yarn-v1.22.11.tar.gz
ARG YARN_VERSION=1.22.11
RUN set -ex \
  && wget --no-check-certificate "https://github.com/yarnpkg/yarn/releases/download/v$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz" -O /yarn.tar.gz \
  && mkdir -vp /usr/local/yarn/ \
  && tar -xzf /yarn.tar.gz -C /usr/local/yarn --strip-components=1 --no-same-owner \
  && ln -s /usr/local/yarn/bin/yarn /usr/local/bin/yarn \
  && ln -s /usr/local/yarn/bin/yarnpkg /usr/local/bin/yarnpkg \
  && rm -v /yarn.tar.gz \
  && yarn config set disable-self-update-check true \
  && yarn global add gulp grunt requirejs

# Create User
ARG RUN_USER=xf
RUN set -ex \
  && groupadd -r ${RUN_USER} --gid 800 \
  && useradd -r --gid ${RUN_USER} --uid 800 --shell /bin/bash ${RUN_USER} \
  && mkdir -vp /home/${RUN_USER} \
  && chown -R ${RUN_USER}:${RUN_USER} /home/${RUN_USER}  

USER ${RUN_USER}

# Test
WORKDIR /tmp/yarn  
COPY ./builder/centos8/arm64 .
RUN set -ex \
  && echo $(arch) \
  && echo $HOME \
  && git --version \
  && java -version \
  && node -v \
  && npm -v \
  && mvn -v  \
  && yarn --version \
  && gulp -v \
  && grunt -h \
  && r.js -v \
  && env \
  && echo "Init node modules via yarn" && yarn install \
  && ls -lh ~/.cache/yarn/ && du -sh ~/.cache/yarn

VOLUME [ "/deploy" ]
WORKDIR /deploy  
CMD ["/bin/bash"]