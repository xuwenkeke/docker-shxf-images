# Introduction
This repository manages Docker images for various services (including TongWeb7, Tomcat8.5, OpenJDK8, CentOS8-based build tools, etc.). It enables multi-architecture (linux/amd64, linux/arm64) automated builds via GitHub Actions and pushes to Docker Hub, providing preconfigured container environments.

# 中文介绍
该仓库管理多种服务的 Docker 镜像（含 TongWeb7、Tomcat8.5、OpenJDK8 及 CentOS8 构建工具等），通过 GitHub Actions 实现多架构（linux/amd64、linux/arm64）自动化构建并推送至 Docker Hub，提供预配置容器环境。

# Docker Image

## [TongWeb](https://hub.docker.com/r/xuwenkeke/tongweb) ![Docker Pulls](https://img.shields.io/docker/pulls/xuwenkeke/tongweb)
Supported architectures `linux/amd64`、`linux/arm64`

| Tag      | Build Status | Image Size |
| :------: | :----------: | :-------: |
| 7.0.8.81-ubuntu22-oraclejdk8 | ![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/xuwenkeke/docker-shxf-images/tongweb-7.0.8.81-ubuntu22-oraclejdk8.yml)  |  ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/xuwenkeke/tongweb/7.0.8.81-ubuntu22-oraclejdk8)  |
| 7.0.4.3-ubuntu22-oraclejdk8 | ![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/xuwenkeke/docker-shxf-images/tongweb-7.0.4.3-ubuntu22-oraclejdk8.yml)  |  ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/xuwenkeke/tongweb/7.0.4.3-ubuntu22-oraclejdk8)  |

## [OpenJDK](https://hub.docker.com/r/xuwenkeke/openjdk) ![Docker Pulls](https://img.shields.io/docker/pulls/xuwenkeke/openjdk)
Supported architectures `linux/amd64`、`linux/arm64`

| Tag      | Build Status | Image Size | Descption |
| :------: | :----------: | :-------: | :---------: |
| jdk8-temurin-noble | ![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/xuwenkeke/docker-shxf-images/openjdk8-temurin.yml)  |  ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/xuwenkeke/openjdk/jdk8-temurin-noble)  | built on  [eclipse-temurin:8u482-b08-jdk-noble](https://hub.docker.com/_/eclipse-temurin/tags?name=8u482-b08-jdk-noble) base image|
| 8-jdk-buster | ![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/xuwenkeke/docker-shxf-images/openjdk8.yml) | ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/xuwenkeke/openjdk/8-jdk-buster) | OpenJDK 1.8.0_302 (The image is **deprecated**) |



