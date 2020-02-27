# Dockerfile for apollo-config-server

# Build with:
# docker build -t apollo-config-server:v1.0.0 .
FROM ubuntu:18.04 AS Jar
WORKDIR /tmp
ARG apollo_version=1.5.1
RUN \
  env && \
  echo ${apollo_version} && \
  sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list && \
  sed -i 's/security.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list && \
  apt-get update -y && \
  apt-get install curl unzip -y &&  \
  curl -L -o apollo-configservice.zip https://github.com/ctripcorp/apollo/releases/download/v${apollo_version}/apollo-configservice-${apollo_version}-github.zip &&  \
  curl -L -o apollo-configservice.zip.sha1 https://github.com/ctripcorp/apollo/releases/download/v${apollo_version}/apollo-configservice-${apollo_version}-github.zip.sha1 &&  \
  unzip ./apollo-configservice.zip &&  \
  cp ./apollo-configservice-1.5.1.jar apollo-configservice.jar

FROM openjdk:8-jre-alpine3.8

RUN \
  echo "http://mirrors.aliyun.com/alpine/v3.8/main" > /etc/apk/repositories && \
  echo "http://mirrors.aliyun.com/alpine/v3.8/community" >> /etc/apk/repositories && \
  apk update upgrade && \
  apk add --no-cache procps curl bash tzdata && \
  ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
  echo "Asia/Shanghai" > /etc/timezone && \
  mkdir -p /apollo-config-server

ADD . /apollo-config-server/
COPY --from=Jar /tmp/apollo-configservice.jar /apollo-config-server/apollo-configservice.jar 

RUN chmod 755 /apollo-config-server/scripts/startup-kubernetes.sh

ENV APOLLO_CONFIG_SERVICE_NAME="service-apollo-config-server.sre"

EXPOSE 8080

CMD ["/apollo-config-server/scripts/startup-kubernetes.sh"]
