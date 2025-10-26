# Installation Guide

## Requirements

### System Requirements
- **OS:** Linux (recommended), Windows, macOS
- **RAM:** Minimum 512MB, recommended 1GB
- **CPU:** Any modern processor
- **Disk:** 100MB for binary + logs + database

### Software Requirements
- **Rust:** 1.70 or newer
- **Minecraft Server:** Any version (Spigot/Paper recommended)
- **RCON:** Enabled on server (optional but recommended)

## Installation Methods

### Method 1: From Source (Recommended)
```bash
# Clone repository
git clone https://github.com/CompileRider/RustGuard
cd rustguard

# Build release version
cargo build --release

# Binary will be at target/release/rustguard
./target/release/rustguard --version
```

### Method 2: Pre-built Binaries
```bash
# Download latest release
wget https://github.com/yourusername/rustguard/releases/latest/download/rustguard-linux-x64

# Make executable
chmod +x rustguard-linux-x64

# Run
./rustguard-linux-x64
```

### Method 3: Using Cargo
```bash
cargo install rustguard

# Run from anywhere
rustguard
```

## Server Configuration

### 1. Minecraft Server Setup

Edit `server.properties`:
```properties
# Change default port (server will be on 25566)
server-port=25566

# Enable RCON
enable-rcon=true
rcon.port=25575
rcon.password=your_secure_password_here

# Optional: whitelist for testing
white-list=true
```

**Restart your Minecraft server**

### 2. RustGuard Configuration

Create `config/config.yaml`:
```yaml
proxy:
  listen_address: "0.0.0.0:25565"  # Players connect here
  server_address: "127.0.0.1:25566"  # Your MC server

rcon:
  enabled: true
  address: "127.0.0.1:25575"
  password: "your_secure_password_here"  # Match server.properties

detection:
  speed_hack:
    enabled: true
    max_speed: 10.8
    confidence_threshold: 0.95
  
  fly_hack:
    enabled: true
    max_jump_height: 1.25

discord:
  enabled: false
  webhook_url: ""  # Optional: add your webhook

database:
  path: "./rustguard.db"

logging:
  level: "info"
```

### 3. First Run
```bash
# Start RustGuard
./rustguard

# You should see:
# 🚀 RustGuard v0.1.0
# 📡 Proxy listening on 0.0.0.0:25565
# 🔗 Connected to server at 127.0.0.1:25566
# ✅ Ready to accept connections
```

### 4. Test Connection
```bash
# In Minecraft, connect to:
# localhost:25565
# (or your_server_ip:25565)
```

## Firewall Configuration

### Linux (UFW)
```bash
# Allow proxy port (public)
sudo ufw allow 25565/tcp

# Server port should stay closed (local only)
sudo ufw deny 25566/tcp

# RCON should stay closed (local only)
sudo ufw deny 25575/tcp
```

### Linux (iptables)
```bash
# Allow proxy
sudo iptables -A INPUT -p tcp --dport 25565 -j ACCEPT

# Block direct server access from outside
sudo iptables -A INPUT -p tcp --dport 25566 ! -s 127.0.0.1 -j DROP
sudo iptables -A INPUT -p tcp --dport 25575 ! -s 127.0.0.1 -j DROP
```

## Systemd Service (Linux)

Create `/etc/systemd/system/rustguard.service`:
```ini
[Unit]
Description=RustGuard Minecraft Anti-Cheat
After=network.target

[Service]
Type=simple
User=minecraft
WorkingDirectory=/opt/rustguard
ExecStart=/opt/rustguard/rustguard
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start:
```bash
sudo systemctl daemon-reload
sudo systemctl enable rustguard
sudo systemctl start rustguard

# Check status
sudo systemctl status rustguard

# View logs
sudo journalctl -u rustguard -f
```

## Docker Installation
```dockerfile
# Dockerfile
FROM rust:1.75 as builder

WORKDIR /app
COPY . .
RUN cargo build --release

FROM debian:bookworm-slim
COPY --from=builder /app/target/release/rustguard /usr/local/bin/
COPY config /etc/rustguard/

EXPOSE 25565
CMD ["rustguard", "--config", "/etc/rustguard/config.yaml"]
```

Run:
```bash
docker build -t rustguard .
docker run -d -p 25565:25565 --name rustguard rustguard
```

## Discord Webhook Setup (Optional)

1. Go to Discord Server Settings → Integrations → Webhooks
2. Create New Webhook
3. Copy webhook URL
4. Add to config:
```yaml
discord:
  enabled: true
  webhook_url: "https://discord.com/api/webhooks/YOUR_WEBHOOK_HERE"
```

Test:
```bash
curl -X POST "YOUR_WEBHOOK_URL" \
  -H "Content-Type: application/json" \
  -d '{"content": "RustGuard test message"}'
```

## Verification

### Check Ports
```bash
# Should show RustGuard on 25565
netstat -tlnp | grep 25565

# Should show MC server on 25566
netstat -tlnp | grep 25566
```

### Test RCON
```bash
# Install mcrcon
sudo apt install mcrcon

# Test connection
mcrcon -H localhost -P 25575 -p your_password "list"
```

### View Logs
```bash
# Real-time logs
tail -f logs/rustguard.log

# Search for specific events
grep "CHEAT DETECTED" logs/rustguard.log
```

## Troubleshooting

### "Address already in use"
```bash
# Check what's using port 25565
sudo lsof -i :25565

# Kill process
sudo kill -9 PID
```

### "Connection refused to server"
```bash
# Verify MC server is running
netstat -tlnp | grep 25566

# Check server logs
tail -f minecraft/logs/latest.log
```

### "RCON authentication failed"
- Verify password matches in both configs
- Restart MC server after changing RCON settings
- Check RCON is enabled in server.properties

### "Permission denied"
```bash
# Make binary executable
chmod +x rustguard

# Check file ownership
ls -la rustguard
```

## Updating
```bash
# Pull latest changes
git pull

# Rebuild
cargo build --release

# Restart service
sudo systemctl restart rustguard
```

## Uninstallation
```bash
# Stop service
sudo systemctl stop rustguard
sudo systemctl disable rustguard

# Remove files
sudo rm -rf /opt/rustguard
sudo rm /etc/systemd/system/rustguard.service

# Remove database (optional)
rm rustguard.db
```

## Next Steps

- [[Configuration|Configure detection settings]]
- [[First-Run|Complete first run guide]]
- [[Integration/RCON-Setup|Advanced RCON setup]]

## Related Documents
- [[Configuration]]
- [[Troubleshooting]]
- [[First-Run]]