## VPS Rebuild Operation Log (20230118)

### Install shadowsocks

1. Install SS:
```python3
pip3 install shadowsocks
```
current version is 2.8.2

2. Configuration
/etc/shadowsocks.json
```
{
  "server": "0.0.0.0",
  "server_port": 8787,
  "password": "pghxif403",
  "method": "aes-256-cfb"
}

```
8087 is user defined port for ss.

### Install kcptun

1. Download
```bash
wget https://github.com/xtaci/kcptun/releases/download/v20221015/kcptun-linux-amd64-20221015.tar.gz
```
2. Extract and install
```bash
cp server_linux_amd64 /usr/local/bin/kcptun
```

### Daemon Service Setup
1. ss service
/etc/systemd/system/shadowsocks.service
```ini
[Unit]
Description=Shadowsocks

[Service]
TimeoutStartSec=0
ExecStart=/usr/local/bin/ssserver -c /etc/shadowsocks.json

[Install]
WantedBy=multi-user.target
```

2. Kcptun service
/usr/lib/systemd/system/kcptun.service
```ini
[Unit]
Description=kcptun-server Service
After=network.target
Wants=network.target

[Service]
Type=simple
PIDFile=/var/run/kcp-server.pid
ExecStart=/usr/local/bin/kcptun -t "127.0.0.1:8787" -l ":29905" -mode fast -key pghxif403 -crypt aes -datashard 10 -parityshard 3 -mtu 1350 -sndwnd 512 -rcvwnd 512 -dscp 0
Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```

3. launch daemon
```bash
systemctl start shadowsocks
systemctl start kcptun
```
