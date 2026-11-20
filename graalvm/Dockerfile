ARG MAJOR_VERSION=25

# Pull GraalVM Community
FROM --platform=$TARGETOS/$TARGETARCH ghcr.io/graalvm/jdk-community:${MAJOR_VERSION} AS graalvm
RUN mkdir -p /opt/graalvm && cp -a "$JAVA_HOME"/. /opt/graalvm/

# Setup Container Runtime
FROM --platform=$TARGETOS/$TARGETARCH redhat/ubi10-minimal:latest
LABEL author="Jamie H." maintainer="contact@jamie.rs"
LABEL org.opencontainers.image.source="https://github.com/summiner/dockerfiles"
LABEL org.opencontainers.image.licenses=MIT

ENV JAVA_HOME=/opt/graalvm
ENV PATH="${JAVA_HOME}/bin:${PATH}"
COPY --from=graalvm /opt/graalvm ${JAVA_HOME}

RUN microdnf update -y \
	&& microdnf install -y lsof curl ca-certificates openssl git tar fontconfig freetype tzdata iproute libstdc++ shadow-utils \
	&& microdnf clean all \
	&& useradd -d /home/container -m container

USER container
ENV USER=container HOME=/home/container

WORKDIR /home/container

COPY ./entrypoint.sh /entrypoint.sh
CMD [ "/bin/bash", "/entrypoint.sh" ]
