# SSH Remote Server Setup Project

This project guides you through setting up SSH access with key-based authentication and securing your server with Fail2Ban.

## 📋 Requirements

- A remote host (server)
- OpenSSH-server installed on the remote host

## 🚀 Setup Instructions

### 1. Generate SSH Key Pairs

Create two RSA key pairs on your local machine:

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_1st_key
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_2nd_key
```

### 2. Copy Public Keys to Remote Server

Transfer both public keys to the remote server:

```bash
ssh-copy-id -i ~/.ssh/id_rsa_1st_key.pub user@server
ssh-copy-id -i ~/.ssh/id_rsa_2nd_key.pub user@server
```

### 3. Test Key-Based Authentication

Verify that you can connect using your keys:

```bash
ssh -i ~/.ssh/id_rsa_1st_key user@server
```

### 4. Configure SSH Server for Key-Only Access

Edit the SSH server configuration on the remote host:

```bash
sudo nano /etc/ssh/sshd_config
```

Update the following settings:

```config
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no  # Disable password authentication
```

Restart the SSH service:

```bash
sudo systemctl restart ssh
```

### 5. Simplify Local SSH Connections

Create or edit your local SSH configuration file:

```bash
nano ~/.ssh/config
```

Add the following configuration:

```config
# Config for first key
Host connect
  HostName server
  User user
  IdentityFile ~/.ssh/id_rsa_1st_key
```

Now you can connect using just: `ssh connect`

## 🔒 Fail2Ban Setup

### Install Fail2Ban

On the remote server (Ubuntu/Debian):

```bash
sudo apt update
sudo apt install fail2ban
```

### Configure Fail2Ban

Copy the default configuration file:

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Edit the configuration:

```bash
sudo vim /etc/fail2ban/jail.local
```

Update the `[sshd]` section:

```config
[sshd]
enabled = true
maxretry = 5
bantime = 10m
findtime = 10m
```

### Start and Enable Fail2Ban

```bash
sudo systemctl start fail2ban
sudo systemctl enable fail2ban
```

### Check Fail2Ban Status

```bash
sudo fail2ban-client status sshd
```

## ✅ Verification

- Test SSH connection using key-based authentication
- Verify password authentication is disabled
- Check that Fail2Ban is running and monitoring SSH attempts
- Test fail2ban by attempting failed logins (use a test environment)

## 📝 Notes

- Always keep backup access methods available when changing SSH configurations
- Store your private keys securely
- Consider using a passphrase for your SSH keys for added security
- Regularly update your server and Fail2Ban configurations

## 🔗 Reference

Based on projects from [roadmap.sh](https://roadmap.sh/projects/ssh-remote-server-setup)