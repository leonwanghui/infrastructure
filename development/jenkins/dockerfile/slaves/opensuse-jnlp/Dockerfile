FROM opensuse/leap:15.1
MAINTAINER tommylike <tommylikehu@gmail.com>

# Install jdk8 and bunch of build tools
USER root
ENV USER root

RUN zypper up -y \
	&& zypper in -y wget \
	&& zypper in -y curl \
	&& zypper in -y git \
	&& zypper in -y subversion

RUN wget -O jre-8u221-linux-x64.rpm -c --content-disposition "https://javadl.oracle.com/webapps/download/AutoDL?BundleId=239847_230deb18db3e4014bb8e3e8324f81b43" \
	&& rpm -ivh jre-8u221-linux-x64.rpm \
	&& rm jre-8u221-linux-x64.rpm

# Setup java path
ENV JAVA_HOME /usr/java/default
ENV PATH $PATH:$JAVA_HOME/bin

# Install jenkins slave
ARG VERSION=3.35
ARG user=jenkins
ARG group=jenkins
ARG uid=1000
ARG gid=1000

RUN groupadd -g ${gid} ${group}
RUN useradd -c "Jenkins user" -d /home/${user} -u ${uid} -g ${gid} -m ${user}

ARG AGENT_WORKDIR=/home/${user}/agent

RUN mkdir -p /usr/share/jenkins/ \
  && cd /usr/share/jenkins/ \
  && wget -O agent.jar https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${VERSION}/remoting-${VERSION}.jar \
  && chmod 755 /usr/share/jenkins \
  && chmod 644 /usr/share/jenkins/agent.jar \
  && ln -sf /usr/share/jenkins/agent.jar /usr/share/jenkins/slave.jar


# Install jnlp
COPY jenkins-agent /usr/local/bin/jenkins-agent
RUN chmod +x /usr/local/bin/jenkins-agent &&\
    ln -s /usr/local/bin/jenkins-agent /usr/local/bin/jenkins-slave


# Setting up env
USER ${user}
ENV AGENT_WORKDIR=${AGENT_WORKDIR}
RUN mkdir /home/${user}/.jenkins && mkdir -p ${AGENT_WORKDIR}

VOLUME /home/${user}/.jenkins
VOLUME ${AGENT_WORKDIR}
WORKDIR /home/${user}

ENTRYPOINT ["jenkins-agent"]
