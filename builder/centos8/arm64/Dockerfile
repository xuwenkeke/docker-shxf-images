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
  && yum makecache \
  && yum install -y git wget \
  && yum clean all \
  && rm -rf /var/cache/yum/*

# Install JDK
ARG JAVA_VERSION=jdk-8u301
RUN set -ex \
  && mkdir -vp /usr/local/java \
  && wget --no-cookies \
      --no-check-certificate \
      --header "Cookie: oraclelicense=accept-securebackup-cookie" \
      "https://download.oracle.com/otn-pub/java/jdk/8u301-b09/d3c52aa6bfa54d3ca74e617f18309292/${JAVA_VERSION}-linux-aarch64.tar.gz" \
      -O /jdk.tar.gz \
  && tar -zxf /jdk.tar.gz -C /usr/local/java/ \
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
ARG MAVEN_INSTALL_DIR=/usr/local/maven
RUN set -ex \
  && mkdir -vp ${MAVEN_INSTALL_DIR} \
  && curl -o /maven.tar.gz "https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/${MAVEN_VERSION}/binaries/apache-maven-${MAVEN_VERSION}-bin.tar.gz" \
  && tar -zxf /maven.tar.gz -C ${MAVEN_INSTALL_DIR} --strip-components=1 --no-same-owner \
  && ls -lh ${MAVEN_INSTALL_DIR} \
  && ln -s ${MAVEN_INSTALL_DIR}/apache-maven-${MAVEN_VERSION} default \
  && ln -s ${MAVEN_INSTALL_DIR}/default/bin/mvn /usr/bin/mvn \
  && rm -v /maven.tar.gz

ENV MAVEN_HOME=${MAVEN_INSTALL_DIR}/default

# Install nodejs
# Download: https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz
ARG NODE_VERSION=14.17.3
ARG NODE_ARCH=arm64
RUN ARCH=$NODE_ARCH \
  && set -ex \
  && curl -o /node.tar.xz "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz" \
  && tar -xJf "node.tar.xz" -C /usr/local --strip-components=1 --no-same-owner \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs \
  && rm -v /node.tar.xz

# Install yarn
# Download: https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz
ENV YARN_VERSION 1.22.5
RUN set -ex \
  && curl -o /yarn.tar.gz "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz" \
  && mkdir -vp /usr/local/yarn/ \
  && tar -xzf /yarn.tar.gz -C /usr/local/yarn \
  && ln -s /usr/local/yarn/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn \
  && ln -s /usr/local/yarn/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg \
  && rm -v /yarn.tar.gz \
  && yarn config set disable-self-update-check true \
  && yarn global add gulp grunt requirejs

# Test
RUN set -ex \
  && echo $(arch) \
  && java -version \
  && node -v \
  && npm -v \
  && mvn -v  \
  && yarn --version \
  && gulp -v \
  && grunt -h \
  && r.js -v

# Create User
ARG RUN_USER=xf
RUN set -ex \
  && groupadd -r ${RUN_USER} --gid 800 \
  && useradd -r --gid ${RUN_USER} --uid 800 --shell /bin/bash ${RUN_USER} \
  && mkdir -vp /home/${RUN_USER} \
  && chown -R ${RUN_USER}:${RUN_USER} /home/${RUN_USER}  

VOLUME [ "/deploy" ]
USER ${RUN_USER}
WORKDIR /deploy  
CMD ["/bin/bash"]