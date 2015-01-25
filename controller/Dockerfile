#
# Dockerfile - NHN nGrinder Controller
#
# - Build
# docker build --rm -t ngrinder:controller /root/docker-nGrinder/controller
#
# - Run
# docker run -d --name="ngrinder-controller" -h "ngrinder-controller" ngrinder:controller
#
# - IP
# docker inspect -f '{{ .NetworkSettings.IPAddress }}' ngrinder-controller
#
#
FROM     ubuntu:14.04
MAINTAINER Yongbok Kim <ruo91@yongbok.net>

# Change the repository
RUN sed -i 's/archive.ubuntu.com/kr.archive.ubuntu.com/g' /etc/apt/sources.list

# Last Package Update & Install
RUN apt-get update && apt-get install -y curl

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

# nGrinder
RUN mkdir $SRC_DIR/nGrinder \
 && curl -L -o /$SRC_DIR/nGrinder/ngrinder-controller-3.3.war \
 "http://downloads.sourceforge.net/project/ngrinder/ngrinder-3.3/ngrinder-controller-3.3.war?r=http%3A%2F%2Fsourceforge.net%2Fprojects%2Fngrinder%2Ffiles%2Fngrinder-3.3%2F&ts=1422129105&use_mirror=cznic"

# Port
EXPOSE 80 16001

# Daemon
ENTRYPOINT ["java", "-XX:MaxPermSize=200m", "-jar", "/opt/nGrinder/ngrinder-controller-3.3.war", "--port", "80"]
