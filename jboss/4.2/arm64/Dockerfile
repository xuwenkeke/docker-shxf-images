FROM arm64v8/openjdk:7-jdk-jessie

ENV LANG=en_US.UTF-8

# Set timezone
ENV TIMEZONE=Asia/Shanghai
RUN set -ex \
  && echo ${TIMEZONE} > /etc/timezone \
  && ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
  && echo ${TIMEZONE} > /etc/TZ
  
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
  && ls -lh \
  && java -version \
  && ls -lh /etc/localtime \
  && env \
  && echo $(dpkg --print-architecture)

EXPOSE 8080/tcp
CMD ["bin/run.sh"]