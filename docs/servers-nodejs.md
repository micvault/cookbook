---
layout: default
title: Setting Up a Node.js Server
parent: 🖥️ Servers
nav_order: 1
permalink: /docs/servers/nodejs
---

# Setting Up a Node.js Server

A complete guide to deploying a Node.js application on a Linux server.

## Details
- **Difficulty:** ⭐⭐ Intermediate
- **Time:** ⏱️ 1-2 hours
- **OS:** Ubuntu/Debian Linux

## Prerequisites

- Linux server (VPS, cloud instance, or local machine)
- SSH access to your server
- Node.js application ready
- Domain name (optional)

## Step 1: SSH & Update System

```bash
# Connect to server
ssh user@your-server-ip

# Update packages
sudo apt update && sudo apt upgrade -y
```

## Step 2: Install Node.js

```bash
# Install Node.js (via NodeSource repository)
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Verify installation
node --version
npm --version
```

## Step 3: Set Up Application

```bash
# Create app directory
mkdir ~/myapp && cd ~/myapp

# Clone your repository (or upload files)
git clone your-repo-url .

# Install dependencies
npm install

# Test run (Ctrl+C to stop)
npm start
```

## Step 4: Use PM2 for Process Management

```bash
# Install PM2 globally
sudo npm install -g pm2

# Start app with PM2
pm2 start app.js --name "myapp"

# Save PM2 config
pm2 save

# Restart on server reboot
pm2 startup
```

## Step 5: Set Up Nginx Reverse Proxy

```bash
# Install Nginx
sudo apt install -y nginx

# Create Nginx config
sudo nano /etc/nginx/sites-available/myapp
```

Add this configuration:
```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_cache_bypass $http_upgrade;
    }
}
```

Then enable it:
```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/myapp /etc/nginx/sites-enabled/

# Test Nginx config
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx
```

## Step 6: Enable HTTPS with Let's Encrypt

```bash
# Install Certbot
sudo apt install -y certbot python3-certbot-nginx

# Get SSL certificate
sudo certbot --nginx -d your-domain.com

# Auto-renewal is enabled by default
```

## Step 7: Monitor & Maintain

```bash
# View PM2 logs
pm2 logs myapp

# Monitor processes
pm2 monit

# Check Nginx status
sudo systemctl status nginx

# View server resources
top
df -h
```

## 💡 Tips & Troubleshooting

| Issue | Solution |
|-------|----------|
| App not starting | Check `pm2 logs myapp` for errors |
| Connection refused | Verify firewall rules, check ports |
| SSL certificate issues | Run `sudo certbot renew --dry-run` |

## Security Best Practices

- 🔒 Use SSH keys instead of passwords
- 🛡️ Enable UFW firewall
- 👤 Create non-root user for app
- 📝 Keep dependencies updated
- 💾 Regular backups

## Useful PM2 Commands

```bash
pm2 list              # Show all processes
pm2 restart myapp     # Restart app
pm2 stop myapp        # Stop app
pm2 delete myapp      # Remove from PM2
pm2 kill              # Stop PM2 daemon
```
