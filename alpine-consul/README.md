# alpine-consul

An image for using [consul][Consul] within Docker containers, bundled with Alpine Linux and [s6][s6].

**_Yet another container for running Consul?_**

Yes, as this one is built from [smebberson/alpine-base][alpinebase] that contains s6 for process management. Small, fast and with s6.

_**Aren't you only supposed to run one process per container?**_

Hell no! The following are good examples of when multiple processes within one container might be necessary:

- Automatically updating [nginx][nginx] proxy settings when a down-stream application server (nodejs, php, etc) restarts (and the IP changes).
- Automatically updating [HAProxy][haproxy] configuration to load balance to a group of down-stream application servers.
- Running a logging daemon to centralize log management (i.e. [logentries][logentries], [loggly][loggly], [logstash][logstash]).
- When you need to run a script on application server crash (to tidy something up), as the standard [Docker container restart policies][drsp] won't provide this.

In all of these instances, there is one primary services and secondary support services. When the secondary support services fail, they should be automatically restarted. When the primary service fails, the container itself should restart.

## Versions

- `2.0.0`, `latest` [(Dockerfile)](https://github.com/smebberson/docker-alpine/blob/master/alpine-consul/Dockerfile)
- `1.1.0` [(Dockerfile)](https://github.com/smebberson/docker-alpine/blob/alpine-consul-v1.1.0/alpine-consul/Dockerfile)
- `1.0.0` [(Dockerfile)](https://github.com/smebberson/docker-alpine/blob/alpine-consul-v1.0.0/alpine-consul/Dockerfile)

[See VERSIONS.md for image contents.](https://github.com/smebberson/docker-alpine/blob/master/alpine-consul/VERSIONS.md)

## Usage

To use this image include `FROM smebberson/alpine-consul` at the top of your `Dockerfile`, or simply `docker run --name consul smebberson/alpine-consul`.

By default, Consul has been configured with:

```
{
    "bootstrap_expect": 1,
    "server": true,
    "node_name": "agent-bootstrapped",
    "data_dir": "/data"
}
```

This setup serves the purpose of a single Consul agent running on a host, mainly for testing. This isn't the recommended scenario, with 3 to 5 total servers per data centre being preferred. However, you can easily customise this by providing your own `config.json` file as discussed below.

Although easily customisable, this image has been designed to run Consul in server mode (without the web ui). If you'd like an image with Consul's web ui, refer to [smebberson/consul-ui][consului].

## Customisation

This container comes setup as follows:

- s6 will automatically start Consul for you
- if Consul dies, it will automatically be restarted

All configuration has been defined in the `root/etc/consul/conf.d/bootstrap/config.json` file (relative to this directory).

To customise configuration for `consul`, replace the file at `root/etc/consul/conf.d/bootstrap/config.json` with your own configuration.

To customise the start script for `consul`, replace the file at `root/etc/services.d/consul/run` with your own start script.

### Consul DNS domain

By default, Consul's DNS domain is `consul.`. This allows you to make DNS queries such as `nginx.service.consul` to find all IPs relating to the `nginx` service (for example). Through customizing the environment variable `CONSUL_DOMAIN` you can alter Consul's DNS domain.

For example, add `ENV CONSUL_DOMAIN=dockeralpine` to your `Dockerfile` and you'll be able to make a DNS query for `nginx.service.dockeralpine` rather than the default.

You can read more about [Consul's DNS interface here][consuldnsinterface].

[s6]: http://www.skarnet.org/software/s6/
[logentries]: https://logentries.com/
[loggly]: https://www.loggly.com/
[logstash]: http://logstash.net/
[drsp]: https://docs.docker.com/reference/commandline/cli/#restart-policies
[nginx]: http://nginx.org/
[haproxy]: http://www.haproxy.org/
[consul]: https://www.consul.io/
[alpinebase]: https://registry.hub.docker.com/u/smebberson/alpine-base/
[consului]: https://github.com/smebberson/docker-ubuntu-base/tree/master/consul-ui
[consulagent]: https://github.com/smebberson/docker-ubuntu-base/tree/master/consul-agent
[consuldnsinterface]: https://www.consul.io/docs/agent/dns.html
