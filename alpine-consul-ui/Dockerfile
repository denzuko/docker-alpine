FROM solvebio/alpine-consul

ENV CONSUL_VERSION=0.7.0

# Download and install Consul UI
RUN apk add --update curl && \
    curl -sSL -o /tmp/consul_web_ui.zip https://releases.hashicorp.com/consul/{$CONSUL_VERSION}/consul_{$CONSUL_VERSION}_web_ui.zip && \
    cd /tmp && \
    unzip consul_web_ui.zip && \
    mkdir /ui && \
    chown -R consul:consul /ui && \
    apk del --purge curl && \
    rm -rf /var/cache/apk/* consul_web_ui.zip

ADD root /

# Expose the web ui port
EXPOSE 8500
