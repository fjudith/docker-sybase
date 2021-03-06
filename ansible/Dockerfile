FROM docker.io/amd64/python:3-alpine

# Metadata params
ARG BUILD_DATE
ARG ANSIBLE_VERSION
ARG ANSIBLE_LINT_VERSION
ARG TINI_VERSION=0.19.0
ARG VCS_REF

LABEL maintainer="Florian JUDITH <florian.judith.b@gmail.com" \
      org.label-schema.url="https://github.com/fjudith/docker-sybase/blob/master/README.md" \
      org.label-schema.build-date="${BUILD_DATE}" \
      org.label-schema.version="${ANSIBLE_VERSION}" \
      org.label-schema.vcs-url="https://github.com/fjudith/docker-sybase.git" \
      org.label-schema.vcs-ref="${VCS_REF}" \
      org.label-schema.docker.dockerfile="ansible/Dockerfile" \
      org.label-schema.description="Ansible and FreeTDS on alpine OCI image" \
      org.label-schema.schema-version="1.0" 

ENV LANG="C.UTF-8" \
    USER="sysops" \
    USERID="10001" \
    GROUP="sysops" \
    GROUPID="10001"

RUN apk add --update --no-cache \
    ca-certificates \
    git \
    openssh-client \
    openssl \
    rsync \
    sshpass \
    gnupg

COPY ansible/files/requirements.txt /tmp/requirements.txt

RUN apk add --update --virtual .build-deps \
        bash \
        libffi-dev \
        openssl-dev \
        build-base \
        curl \
        freetds \
        freetds-dev \
        unixodbc \
        unixodbc-dev \
        gcc \
        libc-dev \
        g++ \
        libffi-dev \
        libxml2 \
        unixodbc-dev \
        mariadb-dev \
        postgresql-dev \
        jq \
        httpie \
    && \
    ln -s /usr/include/locale.h /usr/include/xlocale.h && \
    pip3 install --upgrade -r /tmp/requirements.txt && \
    apk del \
        .build-deps \
        build-base && \
    rm -rf /var/cache/apk/*

ADD https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini /tini
ADD https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini.asc /tini.asc
RUN gpg --batch --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 595E85A6B1B4779EA4DAAEC70B588DFF0527A9B7 && \
    gpg --batch --verify /tini.asc /tini && \
    chmod +x /tini

COPY ansible/files/odbcinst.ini /etc/odbcinst.ini

COPY ansible/scripts/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh
ENTRYPOINT [ "/tini", "--", "/usr/local/bin/docker-entrypoint.sh" ]

WORKDIR /home/${USER}

USER ${USER}