FROM centos:centos8.3.2011

ENV LANG=en_US.UTF-8

# Change yum repositry
RUN set -ex \
  && ls -lh /etc/yum.repos.d \
  && mv /etc/yum.repos.d/CentOS-Linux-BaseOS.repo /etc/yum.repos.d/CentOS-Linux-BaseOS.repo.backup \
  && curl -o /etc/yum.repos.d/CentOS-Linux-BaseOS.repo https://mirrors.aliyun.com/repo/Centos-8.repo \
  && yum makecache \
  && yum install -y vim wget unzip zip glibc-locale-source glibc-langpack-en \
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
      "https://www.dropbox.com/s/5v0wvp7vdy27cp8/${JAVA_VERSION}-linux-$(arch).tar.gz?dl=0" \
      -O /jdk.tar.gz \
  && tar -zxf /jdk.tar.gz -C /usr/local/java/ --strip-components=1 --no-same-owner \
  && sed -i 's/securerandom.source=file:\/dev\/random/securerandom.source=file:\/dev\/.\/urandom/g' /usr/local/java/jre/lib/security/java.security \
  && rm -v /jdk.tar.gz

ENV JAVA_HOME=/usr/local/java
ENV JAVA_BIN=${JAVA_HOME}/bin
ENV PATH=$PATH:$JAVA_BIN
ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# Install JBoss
ARG JBOSS_VERSION=4.2.3.GA
ARG JBOSS_HOME=/usr/local/jboss
RUN set -ex \
  && mkdir -vp ${JBOSS_HOME} \
  && chmod 755 ${JBOSS_HOME} \
  && wget --no-check-certificate "https://nchc.dl.sourceforge.net/project/jboss/JBoss/JBoss-${JBOSS_VERSION}/jboss-${JBOSS_VERSION}.zip" -O /jboss.zip \
  && unzip /jboss.zip -d ${JBOSS_HOME} \
  && mv ${JBOSS_HOME}/jboss*/* ${JBOSS_HOME}/ \
  && rm -v /jboss.zip

# Create User
ARG RUN_USER=xf
RUN set -ex \
  && groupadd -r ${RUN_USER} --gid 800 \
  && useradd -r --gid ${RUN_USER} --uid 800 --shell /bin/bash ${RUN_USER} \
  && mkdir -vp /home/${RUN_USER} \
  && chown -R ${RUN_USER}:${RUN_USER} ${JBOSS_HOME}

# change User
USER ${RUN_USER}
WORKDIR ${JBOSS_HOME}

# Test
RUN set -ex \
  && ls -lh 

EXPOSE 8080/tcp
CMD ["bin/run.sh"]