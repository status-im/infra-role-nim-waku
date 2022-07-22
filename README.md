# Description

This role provisions a [Nim-Waku](https://github.com/status-im/nim-waku) node which speaks the Waku communication protocol.

# Configuration

The crucial settings are:
```yaml
# Private key which affects enode:// address
nim_waku_node_key: 
# Name of Status eth cluster running status-go
nim_waku_bootstrap_fleet: 'test'
nim_waku_log_level: 'info'
```
You can also enable [WebSockets](https://en.wikipedia.org/wiki/WebSocket) as transport for LibP2P using:
```yaml
nim_waku_websoc_port: 443
nim_waku_websocket_enabled: true
nim_waku_websocket_domain: 'hostname.example.org'
nim_waku_websocket_ssl_dir: '/etc/letsencrypt'
nim_waku_websocket_ssl_cert: '/etc/letsencrypt/live/{{ nim_waku_websocket_domain }}/fullchain.pem'
nim_waku_websocket_ssl_key: '/etc/letsencrypt/live/{{ nim_waku_websocket_domain }}/privkey.pem'
```
In this case [LetsEncrypt](https://letsencrypt.org/) is used, but any other certificate would work as long ass the full chain is used.

DNS-based discovery can be enabled to bootstrap a node's connections to a list of existing peers at the configured domain.
The configured domain URL should be in the format 'enrtree://<key>@<fqdn>', for example:
```yaml
nim_waku_dns_disc_enabled: true
nim_waku_dns_disc_url: 'enrtree://AOFTICU2XWDULNLZGRMQS4RIZPAZEHYMV4FYHAPW563HNRAOERP7C@test.waku.nodes.status.im'
```

# Usage

You can re-create containers on the host using:
```
cd /docker/nim-waku-node
docker-compose --compatibility up -d --force-recreate
```
Which will use the `docker-compose.yml` file in that directory.

# Requirements

Due to being part of Status infra this role assumes availability of certain things:

* Docker for running containers
* [Docker user namespace remapping](https://docs.docker.com/engine/security/userns-remap/) with `dockremap` user
* [Watchtower](https://github.com/containrrr/watchtower) for updating Docker images
* The [`iptables-persistent`](https://zertrin.org/projects/iptables-persistent/) module

This role also goes well together with [infra-role-waku-peers](https://github.com/status-im/infra-role-waku-peers).
