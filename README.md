# Advanced WireGuard VPN Server with wgm Command Tool

This advanced cloud-init configuration provides granular control over IP routing and allows you to selectively allow or disallow specific IP ranges when using multiple WireGuard VPNs. It includes a convenient `wgm` command tool for easy management.

## Key Features

### 🔧 **Advanced IP Range Control**
- **Selective Routing**: Control which IP ranges are allowed through the VPN
- **Dynamic Blocking**: Block/unblock specific IP ranges on-the-fly
- **Multiple VPN Support**: Handle multiple VPN networks without conflicts
- **Custom Routing Tables**: Advanced routing with policy-based routing

### 🛡️ **Enhanced Security**
- **Granular Firewall Rules**: Fine-grained control over traffic
- **IP Range Filtering**: Block specific subnets or IP ranges
- **Custom iptables Chains**: Dedicated chains for VPN traffic management

### 🚀 **Easy Management with wgm Command**
- **Simple Commands**: Use `wgm` instead of long management script paths
- **Multiple Access Types**: Configure clients with different access levels
- **Built-in Help**: Comprehensive help system with `wgm help`

## Usage Examples

### 1. Add Client with Different Access Types

#### Full Internet Access (Default)
```bash
# Client routes ALL internet traffic through VPN server
wgm add-client myclient internet
```

#### Private Network Only
```bash
# Client can only access VPN and private networks
wgm add-client myclient private
```

#### Custom IP Ranges
```bash
# Client can access only specific IP ranges
wgm add-client myclient custom '10.0.0.3/32,192.168.0.0/24,100.100.100.0/24'
```

### 2. Service Management

```bash
# Restart WireGuard service
wgm restart

# Start WireGuard service
wgm start

# Stop WireGuard service
wgm stop

# Check service status
wgm status
```

### 3. Network Management

```bash
# Block a specific IP range
wgm block-ip-range '192.168.1.0/24'

# Unblock a previously blocked IP range
wgm unblock-ip-range '192.168.1.0/24'

# Show routing status
wgm routing
```

### 4. Client Management

```bash
# Update client's allowed IP ranges
wgm update-client-ips myclient '10.0.0.3/32,192.168.0.0/24'

# Remove a client
wgm remove-client myclient

# Show client configuration
wgm show-client myclient
```

### 5. Configuration and Troubleshooting

```bash
# Initialize with new keys
wgm init

# Fix configuration issues
wgm fix

# Get help
wgm help
```

## Access Types

### Client Access Configuration

The system supports three different access types for clients:

| Access Type | Internet Access | Private Networks | Custom IPs | Use Case |
|-------------|----------------|------------------|------------|----------|
| `internet` | ✅ Full | ✅ All | ✅ All | Remote workers, general VPN |
| `private` | ❌ None | ✅ VPN + Private | ❌ None | Internal servers, secure access |
| `custom` | ❌ None | ❌ None | ✅ Specified | Fine-grained control |

### Access Type Details

#### **Full Internet Access (`internet`)**
- **Command**: `wg add-client myclient internet`
- **What it does**: Client routes ALL internet traffic through the VPN server
- **Client config**: `AllowedIPs = 0.0.0.0/0`
- **Use case**: Remote workers who want to browse the web through the VPN

#### **Private Network Only (`private`)**
- **Command**: `wg add-client myclient private`
- **What it does**: Client can only access VPN network and private networks
- **Client config**: `AllowedIPs = 10.0.0.0/24,192.168.0.0/24,192.168.1.0/24,100.100.100.0/24`
- **Use case**: Internal servers that only need access to company resources

#### **Custom IP Ranges (`custom`)**
- **Command**: `wg add-client myclient custom '10.0.0.3/32,192.168.0.0/24,100.100.100.0/24'`
- **What it does**: Client can access only specific IP ranges
- **Client config**: `AllowedIPs = 10.0.0.3/32,192.168.0.0/24,100.100.100.0/24`
- **Use case**: Fine-grained control over what the client can access

## Management Commands

### Available Commands

| Command | Description | Example |
|---------|-------------|---------|
| `add-client <name> [access_type] [ips]` | Add client with access type | `add-client myclient internet` |
| `update-client-ips <name> <new_ips>` | Update client's allowed IPs | `update-client-ips myclient '10.0.0.3/32,192.168.0.0/24'` |
| `block-ip-range <range>` | Block specific IP range | `block-ip-range '192.168.1.0/24'` |
| `unblock-ip-range <range>` | Unblock specific IP range | `unblock-ip-range '192.168.1.0/24'` |
| `remove-client <name>` | Remove a client | `remove-client myclient` |
| `routing` | Show routing status | `routing` |
| `show-client <name>` | Show client configuration | `show-client myclient` |
| `restart` | Restart WireGuard service | `restart` |
| `start` | Start WireGuard service | `start` |
| `stop` | Stop WireGuard service | `stop` |
| `status` | Show service status | `status` |
| `fix` | Fix configuration issues | `fix` |
| `init` | Initialize with new keys | `init` |
| `help` | Show help | `help` |

## Advanced Configuration

### Custom Routing Table

The configuration creates a custom routing table (`vpn`) with ID 200:

```bash
# View custom routing table
ip route show table vpn

# View routing rules
ip rule show
```

### Packet Marking

Traffic from specific IP ranges is marked for custom routing:

- Mark 1: `10.0.0.0/24`
- Mark 2: `192.168.0.0/24`  
- Mark 3: `192.168.1.0/24`
- Mark 4: `192.168.5.0/24`

### iptables Chains

Custom chains are created for VPN traffic management:

- `VPN_INPUT` - Input traffic from WireGuard
- `VPN_FORWARD` - Forwarded traffic through WireGuard
- `VPN_OUTPUT` - Output traffic to WireGuard

## Use Cases

### 1. Remote Worker Setup

```bash
# Employee with full internet access through VPN
wgm add-client employee1 internet

# Employee with private network access only
wgm add-client employee2 private
```

### 2. Server Access Control

```bash
# Database server - private network only
wgm add-client db-server private

# Web server - custom access to specific networks
wgm add-client web-server custom '10.0.0.0/24,192.168.1.0/24'

# Admin server - full access
wgm add-client admin-server internet
```

### 3. Multi-Network VPN

When you have multiple networks that need different access levels:

```bash
# Allow access to network A
wgm add-client clientA custom '192.168.0.0/24'

# Allow access to network B  
wgm add-client clientB custom '192.168.1.0/24'

# Block access to network C
wgm block-ip-range '192.168.5.0/24'
```

### 4. Temporary Access Control

```bash
# Temporarily block a network
wgm block-ip-range '192.168.1.0/24'

# Later, unblock it
wgm unblock-ip-range '192.168.1.0/24'
```

### 5. Client-Specific Routing

```bash
# Client 1: Access to development network only
wgm add-client dev1 custom '192.168.0.0/24'

# Client 2: Access to production network only
wgm add-client prod1 custom '192.168.1.0/24'

# Client 3: Access to both networks
wgm add-client admin custom '192.168.0.0/24,192.168.1.0/24'
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

## Migration from Basic Configuration

If you're upgrading from the basic WireGuard configuration:

1. **Backup your current config**:
   ```bash
   sudo cp /etc/wireguard/wg0.conf /etc/wireguard/wg0.conf.backup
   ```

2. **Use the new wgm command**:
   ```bash
   wgm help
   ```

3. **Migrate existing clients**:
   ```bash
   # For each existing client, update with specific access type
   wgm update-client-ips <client_name> <allowed_ips>
   ```

## Quick Start Guide

1. **Deploy the cloud-init configuration**
2. **Check the setup**:
   ```bash
   wgm status
   wg show
   ```

3. **Add your first client**:
   ```bash
   # For full internet access
   wgm add-client myclient internet
   
   # For private network only
   wgm add-client myclient private
   ```

4. **Get help anytime**:
   ```bash
   wgm help
   ```

This advanced configuration provides the flexibility you need for complex networking scenarios while maintaining security and performance. The `wgm` command tool makes management simple and intuitive. 