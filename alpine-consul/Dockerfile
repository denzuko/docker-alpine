FROM smebberson/alpine-base:3.0.0
MAINTAINER Scott Mebberson <scott@scottmebberson.com>

ENV CONSUL_VERSION=0.6.4 \
    CONSUL_DOMAIN=consul \
    CONSUL_CONFIG_DIR=/etc/consul/conf.d/bootstrap \
    CONSUL_SERVER_NAME=consul

# Download and install Consul
RUN apk add --no-cache curl && \
    curl -sSLo /tmp/consul.zip https://releases.hashicorp.com/consul/{$CONSUL_VERSION}/consul_${CONSUL_VERSION}_linux_amd64.zip && \
    unzip -d /bin /tmp/consul.zip && \
    rm /tmp/consul.zip && \
    apk del curl && \
    addgroup consul && \
    adduser -D -g "" -s /bin/sh -G consul consul && \
    mkdir -p /data/consul && \
    chown -R consul:consul /data/consul


# Install GO 1.6
# https://hub.docker.com/r/sephvelu/golang-alpine/~/dockerfile/
# TODO maybe switch to this golang default
# https://github.com/docker-library/golang/blob/master/1.6/alpine/Dockerfile
RUN apk update && \
    apk add curl && \
    curl -L -o glibc-2.21-r2.apk \
      "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-2.21-r2.apk" && \
    curl -L -o glibc-bin-2.21-r2.apk \
      "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-bin-2.21-r2.apk" && \
    apk add --allow-untrusted glibc-2.21-r2.apk glibc-bin-2.21-r2.apk && \
    rm -f glibc-2.21-r2.apk glibc-bin-2.21-r2.apk && \
    /usr/glibc/usr/bin/ldconfig /lib /usr/glibc/usr/lib && \
    curl https://storage.googleapis.com/golang/go1.6.linux-amd64.tar.gz | tar xzf - -C / && \
    mv /go /goroot && \
    mkdir /gopath && \
    mkdir /gopath/src && \
    mkdir /gopath/bin && \
    mkdir /gopath/pkg

ENV GOROOT=/goroot \
    GOPATH=/gopath \
    GOBIN=/gopath/bin \
    PATH=${PATH}:/goroot/bin:/gopath/bin

# Add the files
ADD root /

VOLUME ["/data/consul"]

EXPOSE 8300 8301 8301/udp 8302 8302/udp 8400 8500 53 53/udp
