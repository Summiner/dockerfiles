ARG MAJOR_VERSION=25
ARG ALPINE_VERSION=3.24

# Builder
FROM alpine:${ALPINE_VERSION} AS jdk-builder
ARG MAJOR_VERSION

RUN apk add --no-cache build-base autoconf bash git zip unzip file grep gawk coreutils findutils \
	linux-headers freetype-dev fontconfig-dev zlib-dev alsa-lib-dev cups-dev \
	libxtst-dev libxt-dev libxrender-dev libxrandr-dev openjdk${MAJOR_VERSION}-jdk

RUN set -eux; \
	REPO="https://github.com/openjdk/jdk${MAJOR_VERSION}u"; \
	TAG="$(git ls-remote --tags --refs "${REPO}" "jdk-${MAJOR_VERSION}*" | sed 's|.*refs/tags/||' | sort -V | tail -n1)"; \
	test -n "${TAG}"; \
	SHA="$(git ls-remote --tags "${REPO}" "${TAG}^{}" | cut -f1)"; \
	test -n "${SHA}"; \
	JDK_TAG="$(git ls-remote --tags "${REPO}" 'jdk-*+*' | grep "^${SHA}" | sed 's|.*refs/tags/||; s|\^{}$||' | head -n1)"; \
	test -n "${JDK_TAG}"; \
	echo "${JDK_TAG}" > /jdk-tag; \
	git clone --depth 1 --branch "${JDK_TAG}" "${REPO}.git" /jdk
WORKDIR /jdk

RUN set -eux; \
    bash configure \
        --with-boot-jdk="/usr/lib/jvm/java-${MAJOR_VERSION}-openjdk" \
        --with-jvm-variants=server \
        --with-debug-level=release \
        --with-native-debug-symbols=none \
        --with-extra-cflags="-D_LARGEFILE64_SOURCE" \
        --with-extra-cxxflags="-D_LARGEFILE64_SOURCE" \
        --enable-headless-only=yes \
        --with-version-pre= \
        --with-version-build="$(cut -d+ -f2 /jdk-tag)" \
        --disable-warnings-as-errors \
        --disable-precompiled-headers; \
    make JOBS="$(nproc)" images

RUN set -eux; \
	JDK_IMAGES="$(echo /jdk/build/*/images)"; \
	"${JDK_IMAGES}/jdk/bin/jlink" \
		--module-path "${JDK_IMAGES}/jmods" \
		--add-modules ALL-MODULE-PATH \
		--strip-debug --no-man-pages --no-header-files \
		$([ "${MAJOR_VERSION}" -ge 17 ] && echo --generate-cds-archive) \
		--output /tmp/java

# Container
FROM alpine:3.24

LABEL author="Jamie H." maintainer="contact@jamie.rs"

LABEL org.opencontainers.image.source="https://github.com/summiner/dockerfiles"
LABEL org.opencontainers.image.licenses=MIT

RUN apk add --no-cache curl ca-certificates openssl git tar sqlite fontconfig freetype tzdata iproute2 libstdc++ \
	&& adduser -D -h /home/container -s /sbin/nologin container

COPY --from=jdk-builder /tmp/java /opt/java
ENV JAVA_HOME=/opt/java
ENV PATH="${JAVA_HOME}/bin:${PATH}"

USER container
ENV USER=container HOME=/home/container
WORKDIR /home/container

COPY ./entrypoint.sh /entrypoint.sh
CMD [ "/bin/ash", "/entrypoint.sh" ]
