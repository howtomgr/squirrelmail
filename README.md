# SquirrelMail Installation Guide

SquirrelMail is a free and open-source Webmail. Standards-based webmail package written in PHP

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
  - CPU: 2 cores minimum (4+ cores recommended)
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 1GB for installation
  - Network: 80/443 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80/443 (default squirrelmail port)
- **Dependencies**:
  - php, php-mbstring, php-gettext
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

# Install squirrelmail
sudo dnf install -y squirrelmail php, php-mbstring, php-gettext

# Enable and start service
sudo systemctl enable --now httpd

# Configure firewall
sudo firewall-cmd --permanent --add-service=squirrelmail
sudo firewall-cmd --reload

# Verify installation
squirrelmail --version || systemctl status httpd
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install squirrelmail
sudo apt install -y squirrelmail php, php-mbstring, php-gettext

# Enable and start service
sudo systemctl enable --now httpd

# Configure firewall
sudo ufw allow 80/443

# Verify installation
squirrelmail --version || systemctl status httpd
```

### Arch Linux

```bash
# Install squirrelmail
sudo pacman -S squirrelmail

# Enable and start service
sudo systemctl enable --now httpd

# Verify installation
squirrelmail --version || systemctl status httpd
```

### Alpine Linux

```bash
# Install squirrelmail
apk add --no-cache squirrelmail

# Enable and start service
rc-update add httpd default
rc-service httpd start

# Verify installation
squirrelmail --version || rc-service httpd status
```

### openSUSE/SLES

```bash
# Install squirrelmail
sudo zypper install -y squirrelmail php, php-mbstring, php-gettext

# Enable and start service
sudo systemctl enable --now httpd

# Configure firewall
sudo firewall-cmd --permanent --add-service=squirrelmail
sudo firewall-cmd --reload

# Verify installation
squirrelmail --version || systemctl status httpd
```

### macOS

```bash
# Using Homebrew
brew install squirrelmail

# Start service
brew services start squirrelmail

# Verify installation
squirrelmail --version
```

### FreeBSD

```bash
# Using pkg
pkg install squirrelmail

# Enable in rc.conf
echo 'httpd_enable="YES"' >> /etc/rc.conf

# Start service
service httpd start

# Verify installation
squirrelmail --version || service httpd status
```

### Windows

```powershell
# Using Chocolatey
choco install squirrelmail

# Or using Scoop
scoop install squirrelmail

# Verify installation
squirrelmail --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/squirrelmail

# Set up basic configuration
sudo tee /etc/squirrelmail/squirrelmail.conf << 'EOF'
# SquirrelMail Configuration
session.gc_maxlifetime = 1440
EOF

# Test configuration
sudo squirrelmail -t || sudo httpd configtest

# Reload service
sudo systemctl reload httpd
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R squirrelmail:squirrelmail /etc/squirrelmail
sudo chmod 750 /etc/squirrelmail

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable httpd

# Start service
sudo systemctl start httpd

# Stop service
sudo systemctl stop httpd

# Restart service
sudo systemctl restart httpd

# Reload configuration
sudo systemctl reload httpd

# Check status
sudo systemctl status httpd

# View logs
sudo journalctl -u httpd -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add httpd default

# Start service
rc-service httpd start

# Stop service
rc-service httpd stop

# Restart service
rc-service httpd restart

# Check status
rc-service httpd status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'httpd_enable="YES"' >> /etc/rc.conf

# Start service
service httpd start

# Stop service
service httpd stop

# Restart service
service httpd restart

# Check status
service httpd status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start squirrelmail
brew services stop squirrelmail
brew services restart squirrelmail

# Check status
brew services list | grep squirrelmail
```

### Windows Service Manager

```powershell
# Start service
net start httpd

# Stop service
net stop httpd

# Using PowerShell
Start-Service httpd
Stop-Service httpd
Restart-Service httpd

# Check status
Get-Service httpd
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/squirrelmail/squirrelmail.conf << 'EOF'
session.gc_maxlifetime = 1440
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart httpd
```

### Clustering and High Availability

```bash
# Configure clustering (if supported)
# See official documentation for cluster setup

# Basic load balancing setup example
# Configure multiple instances on different ports
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream squirrelmail_backend {
    server 127.0.0.1:80/443;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name squirrelmail.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name squirrelmail.example.com;

    ssl_certificate /etc/ssl/certs/squirrelmail.example.com.crt;
    ssl_certificate_key /etc/ssl/private/squirrelmail.example.com.key;

    location / {
        proxy_pass http://squirrelmail_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support (if needed)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName squirrelmail.example.com
    Redirect permanent / https://squirrelmail.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName squirrelmail.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/squirrelmail.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/squirrelmail.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/443/
    ProxyPassReverse / http://127.0.0.1:80/443/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:80/443/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend squirrelmail_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/squirrelmail.pem
    redirect scheme https if !{ ssl_fc }
    default_backend squirrelmail_backend

backend squirrelmail_backend
    balance roundrobin
    option httpchk GET /health
    server squirrelmail1 127.0.0.1:80/443 check
    server squirrelmail2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R squirrelmail:squirrelmail /etc/squirrelmail
sudo chmod 750 /etc/squirrelmail

# Configure firewall
sudo firewall-cmd --permanent --add-service=squirrelmail
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/squirrelmail.conf << 'EOF'
[squirrelmail]
enabled = true
port = 80/443
filter = squirrelmail
logpath = /var/log/squirrelmail/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/squirrelmail.key \
    -out /etc/ssl/certs/squirrelmail.crt

# Configure SSL in squirrelmail
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE squirrelmail_db;
CREATE USER squirrelmail_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE squirrelmail_db TO squirrelmail_user;
EOF

# Configure squirrelmail to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE squirrelmail_db;
CREATE USER 'squirrelmail_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON squirrelmail_db.* TO 'squirrelmail_user'@'localhost';
FLUSH PRIVILEGES;
EOF
```

## Performance Optimization

### System Tuning

```bash
# Kernel parameters
sudo tee -a /etc/sysctl.conf << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.core.netdev_max_backlog = 5000
vm.swappiness = 10
EOF

sudo sysctl -p

# SquirrelMail specific tuning
session.gc_maxlifetime = 1440
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
squirrelmail soft nofile 65535
squirrelmail hard nofile 65535
squirrelmail soft nproc 32768
squirrelmail hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'squirrelmail'
    static_configs:
      - targets: ['localhost:80/443']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet httpd; then
    echo "SquirrelMail is running"
    exit 0
else
    echo "SquirrelMail is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/squirrelmail << 'EOF'
/var/log/squirrelmail/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 squirrelmail squirrelmail
    postrotate
        systemctl reload httpd > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# SquirrelMail backup script
BACKUP_DIR="/backup/squirrelmail"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop httpd

# Backup configuration
tar -czf "$BACKUP_DIR/squirrelmail-config-$DATE.tar.gz" /etc/squirrelmail

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/squirrelmail-data-$DATE.tar.gz" /var/lib/squirrelmail

# Start service
systemctl start httpd

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop httpd

# Restore configuration
sudo tar -xzf /backup/squirrelmail/squirrelmail-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/squirrelmail/squirrelmail-data-*.tar.gz -C /

# Set permissions
sudo chown -R squirrelmail:squirrelmail /etc/squirrelmail
sudo chown -R squirrelmail:squirrelmail /var/lib/squirrelmail

# Start service
sudo systemctl start httpd
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u httpd -n 100
sudo tail -f /var/log/squirrelmail/*.log

# Check configuration
sudo squirrelmail -t || sudo httpd configtest

# Check permissions
ls -la /etc/squirrelmail
ls -la /var/lib/squirrelmail
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80/443
sudo netstat -tlnp | grep 80/443

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 80/443
nc -zv localhost 80/443
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep httpd)
htop -p $(pgrep httpd)

# Check connections
ss -ant | grep :80/443 | wc -l

# Monitor I/O
iotop -p $(pgrep httpd)
```

### Debug Mode

```bash
# Run in debug mode
sudo squirrelmail -d
# or
sudo httpd debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  squirrelmail:
    image: squirrelmail:latest
    container_name: squirrelmail
    ports:
      - "80/443:80/443"
    volumes:
      - ./config:/etc/squirrelmail
      - ./data:/var/lib/squirrelmail
    environment:
      - squirrelmail_CONFIG=/etc/squirrelmail/squirrelmail.conf
    restart: unless-stopped
    networks:
      - squirrelmail_net

networks:
  squirrelmail_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: squirrelmail
spec:
  replicas: 3
  selector:
    matchLabels:
      app: squirrelmail
  template:
    metadata:
      labels:
        app: squirrelmail
    spec:
      containers:
      - name: squirrelmail
        image: squirrelmail:latest
        ports:
        - containerPort: 80/443
        volumeMounts:
        - name: config
          mountPath: /etc/squirrelmail
      volumes:
      - name: config
        configMap:
          name: squirrelmail-config
---
apiVersion: v1
kind: Service
metadata:
  name: squirrelmail
spec:
  selector:
    app: squirrelmail
  ports:
  - port: 80/443
    targetPort: 80/443
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure SquirrelMail
  hosts: all
  become: yes
  tasks:
    - name: Install squirrelmail
      package:
        name: squirrelmail
        state: present
    
    - name: Configure squirrelmail
      template:
        src: squirrelmail.conf.j2
        dest: /etc/squirrelmail/squirrelmail.conf
        owner: squirrelmail
        group: squirrelmail
        mode: '0640'
      notify: restart squirrelmail
    
    - name: Start and enable squirrelmail
      systemd:
        name: httpd
        state: started
        enabled: yes
  
  handlers:
    - name: restart squirrelmail
      systemd:
        name: httpd
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update squirrelmail

# Debian/Ubuntu
sudo apt update && sudo apt upgrade squirrelmail

# Arch Linux
sudo pacman -Syu squirrelmail

# Alpine Linux
apk update && apk upgrade squirrelmail

# openSUSE
sudo zypper update squirrelmail

# FreeBSD
pkg update && pkg upgrade squirrelmail

# Always backup before updates
tar -czf /backup/squirrelmail-pre-update-$(date +%Y%m%d).tar.gz /etc/squirrelmail

# Restart after updates
sudo systemctl restart httpd
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/squirrelmail -name "*.log" -mtime +30 -delete

# Verify integrity
sudo squirrelmail --verify || sudo httpd check

# Update databases (if applicable)
sudo squirrelmail-update-db

# Optimize performance
sudo squirrelmail-optimize

# Check for security updates
sudo squirrelmail --security-check
```

## Additional Resources

- Official Documentation: https://docs.squirrelmail.org/
- GitHub Repository: https://github.com/squirrelmail/squirrelmail
- Community Forum: https://forum.squirrelmail.org/
- Wiki: https://wiki.squirrelmail.org/
- Comparison vs Roundcube, RainLoop, Horde, IMP: https://docs.squirrelmail.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
