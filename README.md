# ServerStatus-Rust

基于 [ServerStatus-Rust](https://github.com/zdz/ServerStatus-Rust) 的二次开发版本。

## 项目说明

基于 ServerStatus-Rust 的修改版本，添加了一些自定义功能和改进。

## 许可证

本项目基于 Apache License 2.0 许可证开源。详情请参阅 [LICENSE](./LICENSE) 文件。

## 快速部署

```bash
# 一键部署
mkdir -p /opt/ServerStatus && cd /opt/ServerStatus
wget --no-check-certificate -qO one-touch.sh 'https://raw.githubusercontent.com/surenkid/serverstatus/master/scripts/one-touch.sh'
bash -ex one-touch.sh
```

## 配置说明

### 服务端配置 (`config.toml`)

```toml
grpc_addr = "0.0.0.0:9394"
http_addr = "0.0.0.0:8080"
offline_threshold = 30

admin_user = ""
admin_pass = ""

hosts = [
  {name = "h1", password = "p1", alias = "n1", location = "🏠", type = "kvm"},
]

hosts_group = [
  {gid = "g1", password = "pp", location = "🏠", type = "kvm"},
]
```

### 客户端运行

```bash
# Rust 客户端
./stat_client -a "http://127.0.0.1:8080/report" -u h1 -p p1

# 动态注册
./stat_client -a "http://127.0.0.1:8080/report" -g g1 -p pp --alias "$(hostname)"
```

## 开发说明

### 环境要求

- Rust 1.70 或以上版本

### 编译

```bash
# 安装 Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 编译
git clone https://github.com/surenkid/serverstatus.git
cd serverstatus
cargo build --release
```

## Systemd 服务配置

### 服务端

```bash
cat <<EOF > /etc/systemd/system/stat_server.service
[Unit]
Description=ServerStatus-Rust Server
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/opt/ServerStatus
ExecStart=/opt/ServerStatus/stat_server -c /opt/ServerStatus/config.toml
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable stat_server
systemctl start stat_server
```

### 客户端

```bash
cat <<EOF > /etc/systemd/system/stat_client.service
[Unit]
Description=ServerStatus-Rust Client
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/opt/ServerStatus
ExecStart=/opt/ServerStatus/stat_client -a "http://127.0.0.1:8080/report" -u h1 -p p1
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable stat_client
systemctl start stat_client
```

## 贡献

欢迎提交 Issue 和 Pull Request。
