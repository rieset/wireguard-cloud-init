# WireGuard VPN Server Cloud-Init Configuration

This cloud-init configuration automatically sets up a complete WireGuard VPN server on your cloud instance.

## Features

- **Automatic Installation**: Installs WireGuard and all required dependencies
- **Security**: Configures firewall (UFW) with proper rules
- **Management Tools**: Includes a comprehensive management script
- **Client Management**: Easy client addition and removal
- **Systemd Integration**: Proper service management with systemd

## Supported Cloud Providers

This configuration works with most cloud providers that support cloud-init:
- AWS EC2
- Google Cloud Platform
- DigitalOcean
- Linode
- Vultr
- Azure
- And many others

## Usage

### 1. Launch Your Server

When launching your cloud instance, provide the `cloud-init-wireguard.yml` file as user data:

**AWS EC2 Example:**
```bash
aws ec2 run-instances \
  --image-id ami-xxxxxxxxx \
  --instance-type t3.micro \
  --user-data file://cloud-init-wireguard.yml \
  --security-group-ids sg-xxxxxxxxx \
  --subnet-id subnet-xxxxxxxxx
```

**DigitalOcean Example:**
```bash
doctl compute droplet create wireguard-vpn \
  --image ubuntu-20-04-x64 \
  --size s-1vcpu-1gb \
  --user-data-file cloud-init-wireguard.yml \
  --region nyc1
```

### 2. Wait for Initialization

The server will take a few minutes to complete the setup. You can monitor the progress by checking the cloud-init logs:

```bash
# SSH into your server
ssh user@your-server-ip

# Check cloud-init status
sudo cloud-init status

# View cloud-init logs
sudo cat /var/log/cloud-init-output.log
```

### 3. Access Management Tools

Once setup is complete, you'll have access to the WireGuard management script:

```bash
# View available commands
sudo /usr/local/bin/wg-manage

# Check WireGuard status
sudo /usr/local/bin/wg-manage status

# Add a new client
sudo /usr/local/bin/wg-manage add-client myclient

# Remove a client
sudo /usr/local/bin/wg-manage remove-client myclient
```

## Configuration Details

### Network Configuration
- **VPN Subnet**: 10.0.0.0/24
- **Server IP**: 10.0.0.1
- **WireGuard Port**: 51820/UDP
- **Client IPs**: 10.0.0.2, 10.0.0.3, etc.

### Firewall Rules
- SSH access allowed
- WireGuard port 51820/UDP allowed
- All other incoming traffic denied
- All outgoing traffic allowed

### Files Created
- `/etc/wireguard/wg0.conf` - Main WireGuard configuration
- `/etc/wireguard/server_private.key` - Server private key
- `/etc/wireguard/server_public.key` - Server public key
- `/usr/local/bin/wg-manage` - Management script
- `/usr/local/bin/setup-firewall` - Firewall setup script

## Client Setup

### 1. Add a Client
```bash
sudo /usr/local/bin/wg-manage add-client myclient
```

This will:
- Generate client keys
- Add client to server configuration
- Create client configuration file
- Reload WireGuard service

### 2. Download Client Configuration
The client configuration file will be created at:
```
/etc/wireguard/myclient.conf
```

Download this file to your client device.

### 3. Install WireGuard on Client

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install wireguard
```

**macOS:**
```bash
brew install wireguard-tools
```

**Windows:**
Download from https://www.wireguard.com/install/

### 4. Connect Client
```bash
# Linux/macOS
sudo wg-quick up myclient

# Windows
# Import the .conf file into WireGuard application
```

## Security Considerations

1. **Keep Private Keys Secure**: Never share private keys
2. **Regular Updates**: Keep the server updated
3. **Monitor Logs**: Check WireGuard logs regularly
4. **Backup Configurations**: Backup your WireGuard configurations
5. **Use Strong Passwords**: Ensure SSH access is secure

## Troubleshooting

### Check WireGuard Status
```bash
sudo wg show
sudo systemctl status wireguard@wg0
```

### View Logs
```bash
sudo journalctl -u wireguard@wg0 -f
sudo tail -f /var/log/ufw.log
```

### Common Issues

1. **Port Not Open**: Ensure port 51820/UDP is open in your cloud provider's firewall
2. **Client Can't Connect**: Check if the server's public IP is correct in client config
3. **No Internet Access**: Verify IP forwarding is enabled (`cat /proc/sys/net/ipv4/ip_forward`)

## Customization

### Change VPN Subnet
Edit `/etc/wireguard/wg0.conf` and update the `Address` line:
```
Address = 192.168.1.1/24
```

### Change WireGuard Port
Edit `/etc/wireguard/wg0.conf` and update the `ListenPort` line:
```
ListenPort = 51821
```

Don't forget to update the firewall rules accordingly.

### Add Custom iptables Rules
Modify the `PostUp` and `PostDown` commands in `/etc/wireguard/wg0.conf`.

## Support

For issues with this configuration:
1. Check the cloud-init logs: `/var/log/cloud-init-output.log`
2. Verify WireGuard installation: `which wg`
3. Check firewall status: `sudo ufw status`
4. Review systemd service: `sudo systemctl status wireguard@wg0`

## License

This configuration is provided as-is for educational and personal use. Please ensure compliance with your cloud provider's terms of service and local regulations regarding VPN usage. 