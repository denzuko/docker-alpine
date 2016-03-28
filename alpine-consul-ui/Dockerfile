FROM solvebio/alpine-consul

ENV CONSUL_VERSION=0.6.4

# Download and install Consul-ui
ADD https://releases.hashicorp.com/consul/${CONSUL_VERSION}/consul_${CONSUL_VERSION}_web_ui.zip /tmp/consul_web_ui.zip
RUN mkdir /ui && \
    cd /tmp && \
    unzip -d /ui consul_web_ui.zip && \
    chown -R consul:consul /ui && \
    rm consul_web_ui.zip

ADD root /

# Expose the web ui port
EXPOSE 8500
