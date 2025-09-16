# kraken-ci Installation Guide

kraken-ci is a free and open-source CI/CD system. Kraken provides modern CI/CD system focused on testing

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 2+ cores
  - RAM: 4GB minimum
  - Storage: 50GB for data
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default kraken-ci port)
  - Agent communication
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install kraken-ci
sudo dnf install -y kraken_ci

# Enable and start service
sudo systemctl enable --now kraken-ci

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
kraken-ci --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install kraken-ci
sudo apt install -y kraken_ci

# Enable and start service
sudo systemctl enable --now kraken-ci

# Configure firewall
sudo ufw allow 80

# Verify installation
kraken-ci --version
```

### Arch Linux

```bash
# Install kraken-ci
sudo pacman -S kraken_ci

# Enable and start service
sudo systemctl enable --now kraken-ci

# Verify installation
kraken-ci --version
```

### Alpine Linux

```bash
# Install kraken-ci
apk add --no-cache kraken_ci

# Enable and start service
rc-update add kraken-ci default
rc-service kraken-ci start

# Verify installation
kraken-ci --version
```

### openSUSE/SLES

```bash
# Install kraken-ci
sudo zypper install -y kraken_ci

# Enable and start service
sudo systemctl enable --now kraken-ci

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
kraken-ci --version
```

### macOS

```bash
# Using Homebrew
brew install kraken_ci

# Start service
brew services start kraken_ci

# Verify installation
kraken-ci --version
```

### FreeBSD

```bash
# Using pkg
pkg install kraken_ci

# Enable in rc.conf
echo 'kraken-ci_enable="YES"' >> /etc/rc.conf

# Start service
service kraken-ci start

# Verify installation
kraken-ci --version
```

### Windows

```bash
# Using Chocolatey
choco install kraken_ci

# Or using Scoop
scoop install kraken_ci

# Verify installation
kraken-ci --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/kraken_ci

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
kraken-ci --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable kraken-ci

# Start service
sudo systemctl start kraken-ci

# Stop service
sudo systemctl stop kraken-ci

# Restart service
sudo systemctl restart kraken-ci

# Check status
sudo systemctl status kraken-ci

# View logs
sudo journalctl -u kraken-ci -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add kraken-ci default

# Start service
rc-service kraken-ci start

# Stop service
rc-service kraken-ci stop

# Restart service
rc-service kraken-ci restart

# Check status
rc-service kraken-ci status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'kraken-ci_enable="YES"' >> /etc/rc.conf

# Start service
service kraken-ci start

# Stop service
service kraken-ci stop

# Restart service
service kraken-ci restart

# Check status
service kraken-ci status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start kraken_ci
brew services stop kraken_ci
brew services restart kraken_ci

# Check status
brew services list | grep kraken_ci
```

### Windows Service Manager

```powershell
# Start service
net start kraken-ci

# Stop service
net stop kraken-ci

# Using PowerShell
Start-Service kraken-ci
Stop-Service kraken-ci
Restart-Service kraken-ci

# Check status
Get-Service kraken-ci
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream kraken_ci_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name kraken_ci.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name kraken_ci.example.com;

    ssl_certificate /etc/ssl/certs/kraken_ci.example.com.crt;
    ssl_certificate_key /etc/ssl/private/kraken_ci.example.com.key;

    location / {
        proxy_pass http://kraken_ci_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName kraken_ci.example.com
    Redirect permanent / https://kraken_ci.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName kraken_ci.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/kraken_ci.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/kraken_ci.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend kraken_ci_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/kraken_ci.pem
    redirect scheme https if !{ ssl_fc }
    default_backend kraken_ci_backend

backend kraken_ci_backend
    balance roundrobin
    server kraken_ci1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R kraken_ci:kraken_ci /etc/kraken_ci
sudo chmod 750 /etc/kraken_ci

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status kraken-ci

# View logs
sudo journalctl -u kraken-ci -f

# Monitor resource usage
top -p $(pgrep kraken_ci)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/kraken_ci"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/kraken_ci-backup-$DATE.tar.gz" /etc/kraken_ci /var/lib/kraken_ci

echo "Backup completed: $BACKUP_DIR/kraken_ci-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop kraken-ci

# Restore from backup
tar -xzf /backup/kraken_ci/kraken_ci-backup-*.tar.gz -C /

# Start service
sudo systemctl start kraken-ci
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u kraken-ci -n 100
sudo tail -f /var/log/kraken_ci/kraken_ci.log

# Check configuration
kraken-ci --version

# Check permissions
ls -la /etc/kraken_ci
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep kraken_ci)

# Check disk I/O
iotop -p $(pgrep kraken_ci)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  kraken_ci:
    image: kraken_ci:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/kraken_ci
      - ./data:/var/lib/kraken_ci
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update kraken_ci

# Debian/Ubuntu
sudo apt update && sudo apt upgrade kraken_ci

# Arch Linux
sudo pacman -Syu kraken_ci

# Alpine Linux
apk update && apk upgrade kraken_ci

# openSUSE
sudo zypper update kraken_ci

# FreeBSD
pkg update && pkg upgrade kraken_ci

# Always backup before updates
tar -czf /backup/kraken_ci-pre-update-$(date +%Y%m%d).tar.gz /etc/kraken_ci

# Restart after updates
sudo systemctl restart kraken-ci
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/kraken_ci

# Clean old logs
find /var/log/kraken_ci -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/kraken_ci
```

## Additional Resources

- Official Documentation: https://docs.kraken_ci.org/
- GitHub Repository: https://github.com/kraken_ci/kraken_ci
- Community Forum: https://forum.kraken_ci.org/
- Best Practices Guide: https://docs.kraken_ci.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
