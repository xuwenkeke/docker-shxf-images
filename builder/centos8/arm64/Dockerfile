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
COPY ./files/jdk-8u301-linux-aarch64.tar.gz /usr/local/java/jdk.tar.gz
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
COPY ./files/apache-maven-${MAVEN_VERSION}-bin.tar.gz /usr/local/maven/maven.tar.gz
RUN set -ex \
  && cd /usr/local/maven/ && tar -zxf /usr/local/maven/maven.tar.gz \
  && ln -s /usr/local/maven/apache-maven-${MAVEN_VERSION} default \
  && ln -s /usr/local/maven/default/bin/mvn /usr/bin/mvn \
  && rm /usr/local/maven/maven.tar.gz

ENV MAVEN_HOME=/usr/local/maven/default

# Install nodejs
# Download: https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz
ARG NODE_VERSION=14.17.3
ARG NODE_ARCH=arm64
COPY ./files/node-v${NODE_VERSION}-linux-${NODE_ARCH}.tar.xz /
RUN ARCH=$NODE_ARCH \
  && set -ex \
  && tar -xJf "node-v$NODE_VERSION-linux-$ARCH.tar.xz" -C /usr/local --strip-components=1 --no-same-owner \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs \
  && rm node-v${NODE_VERSION}-linux-${NODE_ARCH}.tar.xz

# Install yarn
# Download: https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz
ENV YARN_VERSION 1.22.5
COPY ./files/yarn-v${YARN_VERSION}.tar.gz /usr/local/yarn/
RUN set -ex \
  && cd /usr/local/yarn/ && tar -xzf yarn-v$YARN_VERSION.tar.gz \
  && ln -s /usr/local/yarn/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn \
  && ln -s /usr/local/yarn/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg \
  && rm yarn-v$YARN_VERSION.tar.gz \
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