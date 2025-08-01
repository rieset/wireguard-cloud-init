# Simple WireGuard VPN Server with wgm Command Tool

This cloud-init configuration provides a simple WireGuard VPN server with two access modes and an easy-to-use `wgm` command tool for management.

## Key Features

### 🌐 **Two Access Modes**
- **Internet Mode**: Full internet access through VPN server
- **Localhost Mode**: Access to localhost (100.100.100.0/24) + private networks
- **Simple Selection**: Choose mode when adding clients

### 🚀 **Easy Management with wgm Command**
- **Simple Commands**: Use `wgm` for all management tasks
- **Automatic Configuration Display**: See client config immediately after creation
- **Built-in Help**: Comprehensive help system with `wgm help`

### 🛡️ **Security & Reliability**
- **Automatic Key Generation**: Secure keys generated automatically
- **Firewall Configuration**: UFW firewall with WireGuard port open
- **Service Management**: Easy start, stop, and restart commands

## Usage Examples

### 1. Add Client with Access Modes

#### Internet Mode (Full Internet Access)
```bash
# Client routes ALL internet traffic through VPN server
wgm add-client myclient internet
```

#### Localhost Mode (Localhost + Private Networks)
```bash
# Client can access localhost (100.100.100.0/24) + private networks
wgm add-client myclient localhost
```

### 2. Client Management

```bash
# Show client configuration (displays full config for copying)
wgm show-client myclient

# Remove a client
wgm remove-client myclient
```

### 3. Service Management

```bash
# Check service status
wgm status

# Restart WireGuard service
wgm restart
```

### 4. Configuration and Help

```bash
# Fix configuration issues
wgm fix

# Get help
wgm help
```

## Access Modes

### Client Access Configuration

The system supports two access modes for clients:

| Access Mode | Internet Access | Localhost (100.100.100.0/24) | Private Networks | Use Case |
|-------------|----------------|------------------------------|------------------|----------|
| `internet` | ✅ Full | ✅ Yes | ✅ Yes | Remote workers, general VPN |
| `localhost` | ❌ None | ✅ Yes | ✅ Yes | Access localhost server + private networks |

### Access Mode Details

#### **🌐 Internet Mode (`internet`)**
- **Command**: `wgm add-client myclient internet`
- **What it does**: Client routes ALL internet traffic through the VPN server
- **Client config**: `AllowedIPs = 0.0.0.0/0`
- **Use case**: When you want to browse the web through the VPN server

#### **🏠 Localhost Mode (`localhost`)**
- **Command**: `wgm add-client myclient localhost`
- **What it does**: Client can access localhost (100.100.100.0/24) + private networks
- **Client config**: `AllowedIPs = 10.0.0.0/24,100.100.100.0/24,192.168.0.0/24,192.168.1.0/24`
- **Use case**: When you want to access localhost server and private networks, but NOT route internet through VPN

## Management Commands

### Available Commands

| Command | Description | Example |
|---------|-------------|---------|
| `add-client <name> <mode>` | Add client with access mode | `add-client myclient internet` |
| `show-client <name>` | Show client configuration | `show-client myclient` |
| `remove-client <name>` | Remove a client | `remove-client myclient` |
| `status` | Show service status | `status` |
| `restart` | Restart WireGuard service | `restart` |
| `fix` | Fix configuration issues | `fix` |
| `help` | Show help | `help` |

## Client Configuration Display

When you add a client, the system automatically displays:

1. **Complete client configuration file** - Ready to copy and paste
2. **Step-by-step connection instructions** - Clear guidance for setup
3. **Mode-specific instructions** - Different instructions for internet vs localhost mode

### Example Output for Internet Mode:
```
=== Client Configuration for myclient ===
[Interface]
PrivateKey = <client_private_key>
Address = 10.0.0.2/24
DNS = 8.8.8.8, 8.8.4.4

[Peer]
PublicKey = <server_public_key>
Endpoint = <server_ip>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
=== End Configuration ===

=== Connection Instructions ===
🌐 INTERNET MODE - Full internet access through VPN
1. Save the configuration above to a file (e.g., myclient.conf)
2. Install WireGuard on your client device
3. Import the configuration file
4. Connect to the VPN
5. All internet traffic will now go through the VPN server
```

### Example Output for Localhost Mode:
```
🏠 LOCALHOST MODE - Access to localhost and private networks
1. Save the configuration above to a file (e.g., myclient.conf)
2. Install WireGuard on your client device
3. Import the configuration file
4. Connect to the VPN
5. You can now access:
   - Localhost server (100.100.100.0/24)
   - Private networks (192.168.0.0/24, 192.168.1.0/24)
   - VPN network (10.0.0.0/24)
6. Internet traffic will NOT go through VPN
```

## Use Cases

### 1. Remote Worker Setup

```bash
# Employee with full internet access through VPN
wgm add-client employee1 internet

# Employee with localhost + private network access only
wgm add-client employee2 localhost
```

### 2. Server Access Control

```bash
# Database server - localhost + private networks only
wgm add-client db-server localhost

# Admin server - full internet access
wgm add-client admin-server internet
```

### 3. Development Environment

```bash
# Developer needs access to localhost server
wgm add-client developer localhost

# QA tester needs full internet access
wgm add-client qa-tester internet
```

### 4. Production vs Development

```bash
# Production server - localhost access only
wgm add-client prod-server localhost

# Development server - full access for testing
wgm add-client dev-server internet
```

## Troubleshooting

### Check Current Rules

```bash
# View all iptables rules
sudo iptables -L -n -v

# View specific chain
sudo iptables -L FORWARD -n -v

# View mangle table
sudo iptables -t mangle -L -n -v
```

### Check Routing

```bash
# View routing tables
ip route show

# View routing rules
ip rule show

# View specific routing table
ip route show table vpn
```

### Debug WireGuard

```bash
# Show WireGuard status
sudo wg show

# Show interface status
ip addr show wg0

# Test connectivity
ping 10.0.0.1
```

### Common Issues

1. **IP Range Not Working**: Check if the range is properly formatted (CIDR notation)
2. **Blocked Traffic**: Verify iptables rules with `iptables -L FORWARD -n`
3. **Routing Issues**: Check routing tables with `ip route show table vpn`
4. **Service Not Starting**: Check logs with `journalctl -u wireguard@wg0 -f`

## Security Considerations

1. **IP Range Validation**: Always validate IP ranges before adding them
2. **Regular Audits**: Periodically review allowed IP ranges
3. **Logging**: Monitor logs for unusual traffic patterns
4. **Backup Configurations**: Keep backups of your WireGuard configurations
5. **Access Control**: Limit who can modify routing rules

## Performance Notes

- Packet marking adds minimal overhead
- Custom routing tables are efficient for large networks
- iptables rules are processed in order (most specific first)
- Consider using `ipset` for large IP range lists

## Quick Start Guide

1. **Deploy the cloud-init configuration**
2. **Check the setup**:
   ```bash
   wgm status
   wg show
   ```

3. **Add your first client**:
   ```bash
   # For full internet access through VPN
   wgm add-client myclient internet
   
   # For localhost + private network access
   wgm add-client myclient localhost
   ```

4. **Copy the displayed configuration to your client device**
5. **Connect using WireGuard client**
6. **Get help anytime**:
   ```bash
   wgm help
   ```

This simple configuration provides two access modes for different use cases while maintaining security and ease of use. The `wgm` command tool makes management straightforward and intuitive. 