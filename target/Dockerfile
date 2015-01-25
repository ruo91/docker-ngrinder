#
# Dockerfile - NHN nGrinder Target (Nginx + nGrinder Monitor)
#
# - Build
# docker build --rm -t ngrinder:target /root/docker-nGrinder/target
#
# - Run
# docker run -d --name="ngrinder-target" -h "ngrinder-target" --link=ngrinder-controller:ngrinder-controller ngrinder:target
#
# - IP
# docker inspect -f '{{ .NetworkSettings.IPAddress }}' ngrinder-target
#
#
FROM     ubuntu:14.04
MAINTAINER Yongbok Kim <ruo91@yongbok.net>

# Change the repository
RUN sed -i 's/archive.ubuntu.com/kr.archive.ubuntu.com/g' /etc/apt/sources.list

# Last Package Update & Install
RUN apt-get update && apt-get install -y curl wget git-core supervisor

# ENV
ENV SRC_DIR /opt

# JDK
ENV JAVA_HOME /usr/local/jdk
ENV PATH $PATH:$JAVA_HOME/bin
RUN cd $SRC_DIR && curl -LO "http://download.oracle.com/otn-pub/java/jdk/7u75-b13/jdk-7u75-linux-x64.tar.gz" -H 'Cookie: oraclelicense=accept-securebackup-cookie' \
 && tar xzf jdk*tar.gz && mv jdk1* $JAVA_HOME && rm -f jdk*.tar.gz \
 && echo '' >> /etc/profile \
 && echo '# JDK' >> /etc/profile \
 && echo "export JAVA_HOME=$JAVA_HOME" >> /etc/profile \
 && echo 'export PATH="$PATH:$JAVA_HOME/bin"' >> /etc/profile \
 && echo '' >> /etc/profile

# nGrinder - Monitor
ENV NGRINDER_MONITOR $SRC_DIR/ngrinder-monitor
ENV PATH $PATH:$NGRINDER_MONITOR
RUN cd $SRC_DIR && git clone https://github.com/ruo91/ngrinder-monitor

# Nginx
RUN apt-get install -y python-software-properties software-properties-common \
 && echo "deb http://nginx.org/packages/ubuntu/ `lsb_release -sc` nginx" > /etc/apt/sources.list.d/nginx.list \
 && wget -q -O- "http://nginx.org/keys/nginx_signing.key" | apt-key add - \
 && apt-get update && apt-get install -y nginx

# Setting for supervisor
RUN mkdir -p /var/log/supervisor
ADD conf/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Port
EXPOSE 80 13243

# Daemon
CMD ["/usr/bin/supervisord"]
