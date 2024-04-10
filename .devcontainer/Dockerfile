ARG UBUNTU_IMG=ubuntu:22.04
#Do not forget also to change "remoteUser" in devcontainer.json
ARG USERNAME=devcon
ARG USERPASS=passw0rd
#Mandrel releases: https://github.com/graalvm/mandrel/releases
ARG MANDREL_LINK=https://github.com/graalvm/mandrel/releases/download/mandrel-23.1.2.0-Final/mandrel-java21-linux-amd64-23.1.2.0-Final.tar.gz
#Set false if you don't need docker. Do not forget to remove --priveleged arg from devcontainer.json
#and comment "mounts" property
ARG IS_DOCKER_NEEDED=true

FROM ${UBUNTU_IMG}
ARG USERPASS
ARG USERNAME
ARG MANDREL_LINK
ENV JAVA_HOME=/lib/mandrel
ENV GRAALVM_HOME=${JAVA_HOME}

## update
RUN apt-get update && apt-get upgrade -y && apt install -y \
    wget \
    curl \
    build-essential \
    git \
    zip \
    unzip \
    libz-dev \
    g++ \
    zlib1g-dev \
    libfreetype6-dev \
    sudo && \
    rm -rf /var/lib/apt/lists/*

#install java-mandrel
WORKDIR /lib
RUN wget ${MANDREL_LINK} -O mandrel.tar.gz && tar -xf mandrel.tar.gz && rm mandrel.tar.gz && \
    mv /lib/$(ls /lib/ | grep mandrel) /lib/mandrel

#Add user
RUN useradd -m -s /bin/bash $USERNAME && \
    echo ${USERNAME}:${USERPASS} | chpasswd && \
    sudo adduser $USERNAME sudo
#Create workspace
RUN mkdir workspace
USER $USERNAME

#Install sdkman && quarkus cli
SHELL ["/bin/bash", "-c"]
RUN curl -s "https://get.sdkman.io" | bash && \
    source "$HOME/.sdkman/bin/sdkman-init.sh" && sdk install quarkus

#Install docker
RUN if [[ -z "$IS_DOCKER_NEEDED" ]] ; then curl -sSL https://get.docker.com | sh && sudo usermod -aG docker $USERNAME ; else echo docker is not needed; fi
    
COPY README.md /workspace/