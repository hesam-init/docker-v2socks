# v2raya · tun2proxy · gost

Dockerized proxy stack that routes **v2raya** traffic through a **tun2proxy** TUN tunnel, backed by a **gost** SOCKS5 relay.

```
gost (SOCKS5 :1080)
  └── tun2proxy (TUN interface)
        └── v2raya (proxy manager UI :2017)
```

## Requirements

- Docker + Docker Compose
- `NET_ADMIN` capability (for TUN device)
- `/dev/net/tun` available on the host

## Usage

```bash
git clone https://github.com/hesam-init/docker-v2socks.git
cd docker-v2socks
docker compose up -d
```

v2raya UI will be available at `http://localhost:2017`.

## Configuration

| Service | Image | Role |
|---|---|---|
| `gost` | `gogost/gost` | SOCKS5 relay on `:1080` |
| `tun2proxy` | `ghcr.io/tun2proxy/tun2proxy-alpine` | Routes traffic through gost via TUN |
| `v2raya` | `mzz2017/v2raya` | Proxy manager, shares tun2proxy network |

### Upstream proxy

Edit the `gost` command in `docker-compose.yml` to point at your upstream:

```yaml
# Shadowsocks
command: -L socks5://:1080 -F sni://138.201.54.122:443

# VMess
command: -L socks5://:1080 -F vmess://uuid@host:port?network=ws

# Plain forward
command: -L socks5://:1080 -F socks5://user:pass@host:port
```

### v2ray vs xray

Switch the binary via `V2RAYA_V2RAY_BIN` in the v2raya environment:

```yaml
- V2RAYA_V2RAY_BIN=/usr/local/bin/v2ray   # default
- V2RAYA_V2RAY_BIN=/usr/local/bin/xray    # xray core
```

## Data

v2raya config is persisted at `./v2raya` (mounted to `/etc/v2raya`).

## Ports

| Port | Service |
|---|---|
| `2017` | v2raya web UI |
| `20170` | v2raya transparent proxy |
