#
# Centos base
#

# Pull base image.
FROM centos:7
MAINTAINER Nicolas Fraison <nfraison@yahoo.fr>

# Environment Path
ENV EPEL_VERSION latest-7

# Java
ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
ENV PATH=${JAVA_HOME}/bin:/opt/hadoop/bin:/opt/spark/bin:/opt/flink/bin:$PATH

# Environment Version
ENV HADOOP_VERSION=2.6.0-cdh5.15.0
ENV SPARK_VERSION=2.3.3
ENV FLINK_VERSION=1.6.4

# Environment Config
ENV HADOOP_CONF_DIR=/opt/hadoop/etc/hadoop
ENV YARN_CONF_DIR=${HADOOP_CONF_DIR}
ENV SPARK_CONF_DIR=/opt/spark/conf
ENV HADOOP_LOG_DIR=/var/log/hadoop

# Add Epel, tooling and update
RUN rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-${EPEL_VERSION}.noarch.rpm && \
    yum update -y && \
    yum install -y wget tar which net-tools curl tcpdump java-1.8.0-openjdk-devel && \
    yum clean all

# Download and unzip hadoop
RUN wget http://archive.cloudera.com/cdh5/cdh/5/hadoop-${HADOOP_VERSION}.tar.gz && \
    tar xvfz hadoop-${HADOOP_VERSION}.tar.gz -C /opt && \
    ln -s /opt/hadoop-${HADOOP_VERSION} /opt/hadoop && \
    rm -f hadoop-${HADOOP_VERSION}.tar.gz

# Download and unzip spark
RUN wget https://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop2.6.tgz && \
    tar xvfz spark-${SPARK_VERSION}-bin-hadoop2.6.tgz -C /opt && \
    ln -s /opt/spark-${SPARK_VERSION}-bin-hadoop2.6 /opt/spark && \
    rm -f spark-${SPARK_VERSION}-bin-hadoop2.6.tgz

# Download and unzip flink
RUN wget http://apache.mediamirrors.org/flink/flink-${FLINK_VERSION}/flink-${FLINK_VERSION}-bin-hadoop26-scala_2.11.tgz && \
    tar xvfz flink-${FLINK_VERSION}-bin-hadoop26-scala_2.11.tgz -C /opt && \
    ln -s /opt/flink-${FLINK_VERSION} /opt/flink && \
    rm -f flink-${FLINK_VERSION}-bin-hadoop26-scala_2.11.tgz

# Create all needed folders
RUN mkdir -p /opt/garmadon/conf-forwarder \
             /opt/garmadon/conf-es-reader \
             /opt/garmadon/lib \
             /data/hdfs /data/yarn ${HADOOP_LOG_DIR}

# Add configurations and entrypoint
ADD hadoop_conf/ ${HADOOP_CONF_DIR}
ADD spark_conf ${SPARK_CONF_DIR}
ADD conf-forwarder/ /opt/garmadon/conf-forwarder
ADD conf-es-reader/ /opt/garmadon/conf-es-reader
ADD conf-hdfs-reader/ /opt/garmadon/conf-hdfs-reader
ADD scripts/ /usr/bin
ADD ressources/ /tmp

RUN chmod a+x /usr/bin/entrypoint.sh /opt/hadoop/etc/hadoop/*.sh && \
    chmod -R 777 /data/hdfs /data/yarn ${HADOOP_LOG_DIR}

# Define default command.
CMD /bin/bash
