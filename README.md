# dokuwiki Installation Guide

dokuwiki is a free and open-source wiki without database. DokuWiki provides simple wiki that stores data in plain text files

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
  - CPU: 1 core minimum
  - RAM: 256MB minimum
  - Storage: 500MB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default dokuwiki port)
  - None
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

# Install dokuwiki
sudo dnf install -y dokuwiki

# Enable and start service
sudo systemctl enable --now dokuwiki

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
dokuwiki --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install dokuwiki
sudo apt install -y dokuwiki

# Enable and start service
sudo systemctl enable --now dokuwiki

# Configure firewall
sudo ufw allow 80

# Verify installation
dokuwiki --version
```

### Arch Linux

```bash
# Install dokuwiki
sudo pacman -S dokuwiki

# Enable and start service
sudo systemctl enable --now dokuwiki

# Verify installation
dokuwiki --version
```

### Alpine Linux

```bash
# Install dokuwiki
apk add --no-cache dokuwiki

# Enable and start service
rc-update add dokuwiki default
rc-service dokuwiki start

# Verify installation
dokuwiki --version
```

### openSUSE/SLES

```bash
# Install dokuwiki
sudo zypper install -y dokuwiki

# Enable and start service
sudo systemctl enable --now dokuwiki

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
dokuwiki --version
```

### macOS

```bash
# Using Homebrew
brew install dokuwiki

# Start service
brew services start dokuwiki

# Verify installation
dokuwiki --version
```

### FreeBSD

```bash
# Using pkg
pkg install dokuwiki

# Enable in rc.conf
echo 'dokuwiki_enable="YES"' >> /etc/rc.conf

# Start service
service dokuwiki start

# Verify installation
dokuwiki --version
```

### Windows

```bash
# Using Chocolatey
choco install dokuwiki

# Or using Scoop
scoop install dokuwiki

# Verify installation
dokuwiki --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/dokuwiki

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
dokuwiki --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable dokuwiki

# Start service
sudo systemctl start dokuwiki

# Stop service
sudo systemctl stop dokuwiki

# Restart service
sudo systemctl restart dokuwiki

# Check status
sudo systemctl status dokuwiki

# View logs
sudo journalctl -u dokuwiki -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add dokuwiki default

# Start service
rc-service dokuwiki start

# Stop service
rc-service dokuwiki stop

# Restart service
rc-service dokuwiki restart

# Check status
rc-service dokuwiki status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'dokuwiki_enable="YES"' >> /etc/rc.conf

# Start service
service dokuwiki start

# Stop service
service dokuwiki stop

# Restart service
service dokuwiki restart

# Check status
service dokuwiki status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start dokuwiki
brew services stop dokuwiki
brew services restart dokuwiki

# Check status
brew services list | grep dokuwiki
```

### Windows Service Manager

```powershell
# Start service
net start dokuwiki

# Stop service
net stop dokuwiki

# Using PowerShell
Start-Service dokuwiki
Stop-Service dokuwiki
Restart-Service dokuwiki

# Check status
Get-Service dokuwiki
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream dokuwiki_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name dokuwiki.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name dokuwiki.example.com;

    ssl_certificate /etc/ssl/certs/dokuwiki.example.com.crt;
    ssl_certificate_key /etc/ssl/private/dokuwiki.example.com.key;

    location / {
        proxy_pass http://dokuwiki_backend;
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
    ServerName dokuwiki.example.com
    Redirect permanent / https://dokuwiki.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName dokuwiki.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/dokuwiki.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/dokuwiki.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend dokuwiki_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/dokuwiki.pem
    redirect scheme https if !{ ssl_fc }
    default_backend dokuwiki_backend

backend dokuwiki_backend
    balance roundrobin
    server dokuwiki1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R dokuwiki:dokuwiki /etc/dokuwiki
sudo chmod 750 /etc/dokuwiki

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
sudo systemctl status dokuwiki

# View logs
sudo journalctl -u dokuwiki -f

# Monitor resource usage
top -p $(pgrep dokuwiki)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/dokuwiki"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/dokuwiki-backup-$DATE.tar.gz" /etc/dokuwiki /var/lib/dokuwiki

echo "Backup completed: $BACKUP_DIR/dokuwiki-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop dokuwiki

# Restore from backup
tar -xzf /backup/dokuwiki/dokuwiki-backup-*.tar.gz -C /

# Start service
sudo systemctl start dokuwiki
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u dokuwiki -n 100
sudo tail -f /var/log/dokuwiki/dokuwiki.log

# Check configuration
dokuwiki --version

# Check permissions
ls -la /etc/dokuwiki
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
top -p $(pgrep dokuwiki)

# Check disk I/O
iotop -p $(pgrep dokuwiki)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  dokuwiki:
    image: dokuwiki:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/dokuwiki
      - ./data:/var/lib/dokuwiki
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update dokuwiki

# Debian/Ubuntu
sudo apt update && sudo apt upgrade dokuwiki

# Arch Linux
sudo pacman -Syu dokuwiki

# Alpine Linux
apk update && apk upgrade dokuwiki

# openSUSE
sudo zypper update dokuwiki

# FreeBSD
pkg update && pkg upgrade dokuwiki

# Always backup before updates
tar -czf /backup/dokuwiki-pre-update-$(date +%Y%m%d).tar.gz /etc/dokuwiki

# Restart after updates
sudo systemctl restart dokuwiki
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/dokuwiki

# Clean old logs
find /var/log/dokuwiki -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/dokuwiki
```

## Additional Resources

- Official Documentation: https://docs.dokuwiki.org/
- GitHub Repository: https://github.com/dokuwiki/dokuwiki
- Community Forum: https://forum.dokuwiki.org/
- Best Practices Guide: https://docs.dokuwiki.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
