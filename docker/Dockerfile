# docker build --platform linux/amd64,linux/arm64 -t geopostcodes/imposm:1 --push .
FROM debian:latest
 
# don't use root as the running user
RUN groupadd -g 999 imposm && \
    useradd -r -u 999 -g imposm imposm
 
# Install base utilities
RUN apt-get update && \
    apt-get install -y wget && \
    apt-get install -y tar gzip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
 
# install imposm
USER 999
WORKDIR /usr/app
RUN wget --quiet https://github.com/omniscale/imposm3/releases/download/v0.11.1/imposm-0.11.1-linux-x86-64.tar.gz && \
    tar -xf imposm-0.11.1-linux-x86-64.tar.gz && \
    mv ./imposm-0.11.1-linux-x86-64/* . && \
    rm -rf ./imposm-0.11.1-linux-x86-64 && \
    rm imposm-0.11.1-linux-x86-64.tar.gz