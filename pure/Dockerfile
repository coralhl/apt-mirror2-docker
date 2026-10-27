# Similar as the initial build in https://gitlab.com/apt-mirror2/apt-mirror2/-/blob/master/Dockerfile
FROM python:3.13-alpine AS builder

SHELL ["/bin/sh", "-ex", "-c"]

ARG APT_MIRROR_V=16

# add httpx[socks]
RUN apk update && \
    apk upgrade && \
    apk add --no-cache \
        binutils \
        gcc \
        linux-headers \
        musl-dev && \
    rm -rf /var/cache/apk/* && \
    apk cache clean && \
    wget https://gitlab.com/apt-mirror2/apt-mirror2/-/archive/v${APT_MIRROR_V}/apt-mirror2-v${APT_MIRROR_V}.tar.gz && \
    mkdir -p /tmp/apt-mirror2/ && \
    tar -xzf apt-mirror2-v${APT_MIRROR_V}.tar.gz --strip-components=1 -C /tmp/apt-mirror2/ && \
    rm -rf apt-mirror2-v${APT_MIRROR_V}.tar.gz && \
    cd /tmp/apt-mirror2 && \
    pip --disable-pip-version-check --no-cache-dir install \
        -r requirements.txt \
        -r requirements/dev.txt && \
    pip install . && \
    pip install socksio "httpx[socks]" && \
    cd / && \
    rm -rf /tmp/apt-mirror2 && \
    pyinstaller \
        --clean \
        --onefile \
        --noconfirm \
        --copy-metadata aioftp \
        --name apt-mirror \
        /usr/local/bin/apt-mirror

FROM alpine:3.24

SHELL ["/bin/sh", "-ex", "-c"]

COPY --from=builder /dist/apt-mirror /usr/local/bin/apt-mirror

RUN apk update && \
    apk upgrade && \
    apk add --no-cache gpgv tzdata && \
    rm -rf /var/cache/apk/* && \
    apk cache clean

CMD [ ]

ENTRYPOINT [ "apt-mirror" ]
