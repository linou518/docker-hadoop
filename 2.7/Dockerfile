########################################################################
# Dockerfile for Hadoop 2.7 on JDK 8
#
#                    ##        .
#              ## ## ##       ==
#           ## ## ## ##      ===
#       /""""""""""""""""\___/ ===
#  ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
#       \______ o          __/
#         \    \        __/
#          \____\______/
#
# Component:    Hadoop 2.7
# Author:       pjan vandaele <pjan@pjan.io>
# Scm url:      https://github.com/pjan/docker-hadoop
# License:      MIT
########################################################################

# pull base image
FROM docxs/java:8

# maintainer details
MAINTAINER \
  pjan vandaele "pjan@pjan.io"

# set environment variables
ENV HADOOP_VERSION_MAJOR  2
ENV HADOOP_VERSION_MINOR  7
ENV HADOOP_VERSION_BUILD  0
ENV HADOOP_VERSION ${HADOOP_VERSION_MAJOR}.${HADOOP_VERSION_MINOR}.${HADOOP_VERSION_BUILD}
ENV HADOOP_PREFIX /opt/hadoop
ENV HADOOP_HOME /opt/hadoop

# Add the ssh config file
COPY config/root_ssh_config /src/config/root_ssh_config

# Install Hadoop
RUN apt-prepare \
 && apt-get install -y \
      apt-utils \
      curl \
      tar \
      sudo \
      openssh-server \
      openssh-client \
      rsync \
 && echo "setting up passwordless ssh ..." \
 && rm /etc/ssh/ssh_host_dsa_key /etc/ssh/ssh_host_rsa_key \
 && ssh-keygen -q -N "" -t dsa -f /etc/ssh/ssh_host_dsa_key \
 && ssh-keygen -q -N "" -t rsa -f /etc/ssh/ssh_host_rsa_key \
 && ssh-keygen -q -N "" -t rsa -f /root/.ssh/id_rsa \
 && cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys \
 && echo -n "Downloading the native support libraries ..." \
 && curl -jksSL http://dl.bintray.com/docxs/docxs-bin/hadoop-native-debian-64_${HADOOP_VERSION}.tar.gz | tar -xzf - -C /tmp/ && echo " done" \
 && echo -n "Getting hadoop ..." \
 && curl -jksSL http://www.eu.apache.org/dist/hadoop/common/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz | tar -xzf - -C /opt && echo " done" \
 && ln -s /opt/hadoop-${HADOOP_VERSION} $HADOOP_HOME \
 && echo "Configuring Hadoop ..." \
 && sed -i "/^export JAVA_HOME/ s:.*:export JAVA_HOME=$JAVA_HOME\nexport HADOOP_PREFIX=$HADOOP_PREFIX\nexport HADOOP_HOME=$HADOOP_HOME\n:" $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh \
 && sed -i "/^export HADOOP_CONF_DIR/ s:.*:export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop/:" $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh \
 && mkdir $HADOOP_PREFIX/input \
 && cp $HADOOP_PREFIX/etc/hadoop/*.xml $HADOOP_PREFIX/input \
 && echo "fixing the native support libraries with the downloaded ones ..." \
 && rm $HADOOP_PREFIX/lib/native/* \
 && cp -R /tmp/hadoop-native-debian-64_2.7.0/* $HADOOP_PREFIX/lib/native/ \
 && rm -rf /tmp/hadoop-native-debian-64_2.7.0 \
 && echo "configuring ssh ..." \
 && cp -f /src/config/root_ssh_config /root/.ssh/config \
 && chmod 600 /root/.ssh/config \
 && chown root:root /root/.ssh/config \
 && echo "workaround for a docker build error ..." \
 && echo "updating permissions ..." \
 && ls -la $HADOOP_PREFIX/etc/hadoop/*-env.sh \
 && chmod +x $HADOOP_PREFIX/etc/hadoop/*-env.sh \
 && ls -la $HADOOP_PREFIX/etc/hadoop/*-env.sh \
 && echo "fix the 254 error code ..." \
 && sed  -i "/^[^#]*UsePAM/ s/.*/#&/"  /etc/ssh/sshd_config \
 && echo "UsePAM no" >> /etc/ssh/sshd_config \
 && echo "Port 2122" >> /etc/ssh/sshd_config \
 && apt-cleanup

# Set additional environment variables
ENV PATH $PATH:$HADOOP_PREFIX/bin

# Add configuration files
ADD config /src/config

# Add the binary files
ADD bin /bin/

# expose the relevant ports
# HDFS:  50010 50020 50070 50075 50090
# Mapred: 19888
# Yarn: 8030 8031 8032 8033 8040 8042 8088
# Other: 49707 2122
EXPOSE \
  50010 50020 50070 50075 50090 19888 8030 8031 8032 8033 8040 8042 8088 49707 2122

# Run bootstrap on container launch
ENTRYPOINT \
  ["bootstrap"]

# Default command
CMD \
  ["-d"]
