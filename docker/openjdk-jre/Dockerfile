FROM docker.io/centos:7

ENV JAVA_HOME="/usr/java"
ENV D_ENV_Java_version="1.8.0"
ENV HOME="/opt/java-apps"

RUN echo "---> Installing java-${D_ENV_Java_version}-openjdk" && \
  yum install -y java-${D_ENV_Java_version}-openjdk && \
  yum clean all && \
  ln -s $(readlink -f /usr/bin/java | sed "s:bin/java::") $JAVA_HOME && \
  java -version && \
  mkdir -p ${HOME}
