
FROM ubuntu:latest

WORKDIR /home

RUN apt update \
    && apt -y install software-properties-common ssh openssh-server

#Install Open JDK
RUN apt -y install openjdk-8-jdk \
    && update-java-alternatives -s java-1.8.0-openjdk-amd64

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre/


#Copy hadoop
COPY hadoop-2.8.2.tar.gz /usr/local/
WORKDIR /usr/local/
RUN tar -xf hadoop-2.8.2.tar.gz \
    && rm hadoop-2.8.2.tar.gz \
    && ln -s ./hadoop-2.8.2 hadoop

ENV PATH="/usr/local/hadoop/bin:${PATH}"

RUN ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa \
    && cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys \
    && chmod 0600 ~/.ssh/authorized_keys

COPY hadoop-env.sh /usr/local/hadoop/etc/hadoop/
COPY hdfs-site.xml /usr/local/hadoop/etc/hadoop/
COPY core-site.xml /usr/local/hadoop/etc/hadoop/

#Configure map reduce for YARN
COPY mapred-site.xml /usr/local/hadoop/etc/hadoop/
COPY yarn-site.xml /usr/local/hadoop/etc/hadoop/

WORKDIR /bin/

#Bypass interactive prompt
RUN echo "Host *"  >>/root/.ssh/config
RUN echo "StrictHostKeyChecking no" >> /root/.ssh/config

ENV HADOOP_HOME=/usr/local/hadoop/
ENV HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop

ADD bootstrap.sh /bin/
RUN chmod +x bootstrap.sh

CMD ["/bin/bootstrap.sh"]

#Name node dir
RUN mkdir -p /usr/data/hdfs/namenode/
RUN mkdir -p /usr/data/hdfs/datanode/

RUN mkdir -p /tmp/hadoop-root/dfs/name

#Format if necesssary
#RUN /usr/local/hadoop/bin/hdfs namenode -format


#Not required to expose everything 
#EXPOSE 50020 50090 50070 50010 50075 8030 8031 8032 8033 8040 8042 49707 22 8088 9870 9000
#EXPOSE 10020 38767

#Expose only required ones
#UI, HDFS, Web
EXPOSE 50070 9000 8088

#Yarn UI
EXPOSE 50030 50060

#Debug

#RUN apt-get install -y xinetd telnetd
#RUN apt-get install -y net-tools iputils-ping
