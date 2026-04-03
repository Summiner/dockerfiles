ARG GRAALVM_VERSION=25
FROM  --platform=$TARGETOS/$TARGETARCH eclipse-temurin:${GRAALVM_VERSION}

LABEL author="Jamie H." maintainer="jamie@blze.gg"

LABEL       org.opencontainers.image.source="https://github.com/summiner/dockerfiles"
LABEL       org.opencontainers.image.licenses=MIT

RUN 		apt-get update -y \
				&& apt-get install -y lsof curl ca-certificates openssl git tar sqlite3 fontconfig libfreetype6 tzdata iproute2 libstdc++6 \
				&& useradd -d /home/container -m container

USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

COPY        ./entrypoint.sh /entrypoint.sh

CMD         [ "/bin/bash", "/entrypoint.sh" ]
