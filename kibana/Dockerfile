#FROM debian:jessie

FROM java:openjdk-7-jdk

RUN apt-get update && \
    apt-get install -y openjdk-7-jre wget

ENV JAVA_HOME /usr/lib/jvm/java-6-openjdk-amd64
ENV KB_VERSION 4.0.2

RUN apt-get update && apt-get -y upgrade
RUN apt-get -y install wget 

WORKDIR /opt

RUN wget --no-check-certificate -O- https://download.elasticsearch.org/kibana/kibana/kibana-$KB_VERSION-linux-x64.tar.gz | tar xvfz -
RUN mv /opt/kibana-$KB_VERSION-linux-x64 /opt/kibana

# set up the command to start kibana, this is more complex than it should be.
ADD ./start.sh /start.sh
RUN chmod +x /start.sh

CMD '/start.sh'

EXPOSE 5601

