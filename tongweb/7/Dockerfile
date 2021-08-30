FROM centos:centos8.3.2011

ENV LANG=en_US.UTF-8

# Change yum repositry
RUN set -ex \
  && ls -lh /etc/yum.repos.d \
  && mv /etc/yum.repos.d/CentOS-Linux-BaseOS.repo /etc/yum.repos.d/CentOS-Linux-BaseOS.repo.backup \
  && curl -o /etc/yum.repos.d/CentOS-Linux-BaseOS.repo https://mirrors.aliyun.com/repo/Centos-8.repo \
  && yum makecache \
  && yum install -y vim wget glibc-locale-source glibc-langpack-en \
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
  && echo `pwd` \
  && wget --quiet --no-check-certificate "https://www.dropbox.com/s/wbdzd5x8asmkr69/TongWeb7.0.4.3_Enter_Linux.tar.gz?dl=0" -O ./install/TongWeb_Enter_Linux.tar.gz \
  && tar -zxf ./install/TongWeb_Enter_Linux.tar.gz -C ./tongweb --no-same-owner \
  && cp ./install/license.dat tongweb/license.dat


# Run Tongweb
ENV TONGWEB_HOME=/home/${RUN_USER}/tongweb
ENV TONGWEB_VERSION=7.0.4.3

WORKDIR ${TONGWEB_HOME}
RUN set -ex \
  && ls -lh \
  && sh bin/version.sh

EXPOSE 8088/tcp
EXPOSE 9060/tcp
VOLUME [ "/home/${RUN_USER}/tongweb/logs" ]
VOLUME [ "/home/${RUN_USER}/tongweb/bin/external.vmoptions" ]
VOLUME [ "/home/${RUN_USER}/tongweb/conf/tongweb.xml" ]

CMD ["bin/startserver.sh"]