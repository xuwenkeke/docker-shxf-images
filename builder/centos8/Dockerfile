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
  && mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
  && curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo \
  && sed -i 's/mirrors.cloud.aliyuncs.com/mirrors.aliyun.com/g' /etc/yum.repos.d/CentOS-Base.repo \
  && yum clean all && yum makecache

# Install Git 
RUN set -ex \
  && yum install -y git \
  && git -version

