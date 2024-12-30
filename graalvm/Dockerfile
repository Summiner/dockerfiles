ARG GRAALVM_VERSION=25
FROM  --platform=$TARGETOS/$TARGETARCH ghcr.io/graalvm/jdk-community:${GRAALVM_VERSION}

LABEL author="Jamie H." maintainer="jamie@blze.gg"

LABEL       org.opencontainers.image.source="https://github.com/summiner/dockerfiles"
LABEL       org.opencontainers.image.licenses=MIT

RUN   microdnf update -y \
      && microdnf install -y lsof curl ca-certificates openssl git tar fontconfig freetype tzdata iproute libstdc++ \
      && useradd -d /home/container -m container

USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

COPY        ./entrypoint.sh /entrypoint.sh

CMD         [ "/bin/bash", "/entrypoint.sh" ]
