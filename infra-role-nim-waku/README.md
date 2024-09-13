# Description

This role provisions a [Nim-Waku](https://github.com/status-im/nim-waku) node which speaks the Waku communication protocol.

# Configuration

The crucial settings are:
```yaml
# Private key which affects enode:// address
nim_waku_node_key: 
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
REST API is avalable to inspect the node and use protocols:
```yaml
nim_waku_rest_enabled: true
nim_waku_rest_addr: '127.0.0.1'
nim_waku_rest_port: 8645
nim_waku_rest_apis_enabled: ['admin', 'private']
```
For full docs see [API docs page](https://waku-org.github.io/waku-rest-api/).

There's also a [container monitor service](./MONITOR.md).
```yaml
nim_waku_monitor_enabled: true
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




# 前置安装

## 安装docker-compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
## 添加用和组
sudo groupadd docker
sudo useradd -m -g docker dockremap



# 部署命令
<!-- ansible-playbook -i inventory tasks/docker.yml --ask-become-pass -->
ansible-playbook -i inventory playbook.yml --ask-become-pass




# 节点相关

## 官方节点信息

从 https://fleets.waku.org  获取

/dns4/node-01.do-ams3.wakuv2.prod.status.im/tcp/30303/p2p/16Uiu2HAmL5okWopX7NqZWBUKVqW8iUxCEmd5GMHLVPwCgzYzQv3e
/dns4/node-01.do-ams3.wakuv2.prod.status.im/tcp/8000/wss/p2p/16Uiu2HAmL5okWopX7NqZWBUKVqW8iUxCEmd5GMHLVPwCgzYzQv3e

## 连接节点
/usr/bin/wakunode --staticnode="/dns4/node-01.do-ams3.wakuv2.prod.status.im/tcp/30303/p2p/16Uiu2HAmL5okWopX7NqZWBUKVqW8iUxCEmd5GMHLVPwCgzYzQv3e" 



##
curl -X GET "http://127.0.0.1:8645/admin/v1/peers" | jq

curl -X GET "http://127.0.0.1:8645/debug/v1/info"  | jq


# err log


```
TASK [infra-role-nim-waku : Create container: nim-waku] ************************************************************************************************************************************************
fatal: [web2]: FAILED! => {"changed": false, "cmd": 

"/snap/bin/docker --host unix:///var/run/docker.sock compose
 --ansi never 
 --progress plain 
 --project-directory /docker/nim-waku 
 ps --format json --all",
 
  "msg": "no configuration file provided: not found", 
  
  "rc": 14, "stderr": "no configuration file provided: not found\n", "stderr_lines": ["no configuration file provided: not found"], "stdout": "", "stdout_lines": []}
```