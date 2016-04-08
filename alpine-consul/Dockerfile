FROM smebberson/alpine-base:1.3.0
MAINTAINER Scott Mebberson <scott@scottmebberson.com>

ENV CONSUL_VERSION=0.6.4 GOMAXPROCS=2

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

# Download and install Consul
# We are building it ourselves, because Alpine Linux doesn't use glibc
RUN apk add --update git gcc musl-dev make bash zip && \
    go get github.com/hashicorp/consul && \
    cd $GOPATH/src/github.com/hashicorp/consul && \
    git checkout -q --detach "v$CONSUL_VERSION" && \
    make && \
    mv bin/consul /bin && \
    rm -rf $GOPATH && \
    apk del go git gcc musl-dev make bash && \
    rm -rf /var/cache/apk/* && \
    addgroup consul && \
    adduser -D -G consul consul && \
    mkdir -p /data/consul && \
    chown -R consul:consul /data/consul

# Add the files
ADD root /

VOLUME ["/data/consul"]

EXPOSE 8300 8301 8301/udp 8302 8302/udp 8400 53 53/udp
