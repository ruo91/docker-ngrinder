#
# Dockerfile - NHN nGrinder Agent
#
# - Build
# docker build --rm -t ngrinder:agent /opt/docker-nGrinder/agent
#
# - Run
# docker run -d --name="ngrinder-agent-1" -h "ngrinder-agent-1" --link=ngrinder-controller:ngrinder-controller ngrinder:agent
# docker run -d --name="ngrinder-agent-2" -h "ngrinder-agent-2" --link=ngrinder-controller:ngrinder-controller ngrinder:agent
# docker run -d --name="ngrinder-agent-3" -h "ngrinder-agent-3" --link=ngrinder-controller:ngrinder-controller ngrinder:agent
# docker run -d --name="ngrinder-agent-4" -h "ngrinder-agent-4" --link=ngrinder-controller:ngrinder-controller ngrinder:agent
#
# - IP
# docker inspect -f '{{ .NetworkSettings.IPAddress }}' ngrinder-agent
#
#
FROM     ubuntu:14.04
MAINTAINER Yongbok Kim <ruo91@yongbok.net>

# Change the repository
RUN sed -i 's/archive.ubuntu.com/kr.archive.ubuntu.com/g' /etc/apt/sources.list

# Last Package Update & Install
RUN apt-get update && apt-get install -y curl git-core

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

# nGrinder - Agent
ENV NGRINDER_AGENT $SRC_DIR/ngrinder-agent
ENV PATH $PATH:$NGRINDER_AGENT
RUN cd $SRC_DIR && git clone https://github.com/ruo91/ngrinder-agent
ADD conf/__agent.conf	$NGRINDER_AGENT/__agent.conf

# Daemon
ENTRYPOINT ["run_agent.sh"]
