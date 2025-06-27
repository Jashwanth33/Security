MAC Address Changer - Kali Linux

A comprehensive guide for changing MAC addresses in Kali Linux using terminal commands for network administration and penetration testing purposes.

Table of Contents
- Overview
- Prerequisites
- Methods
- Usage Examples
- Scripts
- Troubleshooting
- Legal Disclaimer

Overview

MAC Media Access Control address changing is a technique used in network administration, privacy protection, and penetration testing. This repository provides methods to change MAC addresses in Kali Linux using built-in tools.

Use Cases:
- Network penetration testing
- Privacy protection
- Bypassing MAC address filtering
- Network troubleshooting
- Security research

Prerequisites

- Kali Linux operating system
- Root or sudo privileges
- Network interface available
- Basic understanding of networking concepts

Methods

1. Using macchanger Recommended

Kali Linux comes with macchanger pre-installed, which is the most reliable method.

Installation if not present
```bash
sudo apt update
sudo apt install macchanger
```

Basic Commands
```bash
macchanger -s eth0
sudo macchanger -r eth0
sudo macchanger -m 00:11:22:33:44:55 eth0
sudo macchanger -p eth0
```

2. Using ifconfig

Traditional method using ifconfig command.

```bash
sudo ifconfig eth0 down
sudo ifconfig eth0 hw ether 00:11:22:33:44:55
sudo ifconfig eth0 up
```

3. Using ip command

Modern method using the ip command.

```bash
sudo ip link set dev eth0 down
sudo ip link set dev eth0 address 00:11:22:33:44:55
sudo ip link set dev eth0 up
```

Usage Examples

Example 1 Random MAC Address
```bash
#!/bin/bash
sudo ifconfig eth0 down
sudo macchanger -r eth0
sudo ifconfig eth0 up
echo "MAC address changed to random address"
```

Example 2 Specific Vendor MAC
```bash
#!/bin/bash
sudo ifconfig eth0 down
sudo macchanger -m 00:1B:63:84:45:E6 eth0
sudo ifconfig eth0 up
echo "MAC address changed to Apple vendor MAC"
```

Example 3 Restore Original MAC
```bash
#!/bin/bash
sudo ifconfig eth0 down
sudo macchanger -p eth0
sudo ifconfig eth0 up
echo "Original MAC address restored"
```

Scripts

mac_changer.sh
```bash
#!/bin/bash

INTERFACE=${1:-eth0}
OPTION=${2:-random}

check_interface() {
    if ! ip link show "$INTERFACE" > /dev/null 2>&1; then
        echo "Error: Interface $INTERFACE does not exist"
        exit 1
    fi
}

change_mac() {
    echo "Bringing down interface $INTERFACE..."
    sudo ip link set dev "$INTERFACE" down
    
    case $OPTION in
        "random")
            echo "Changing to random MAC address..."
            sudo macchanger -r "$INTERFACE"
            ;;
        "reset")
            echo "Resetting to original MAC address..."
            sudo macchanger -p "$INTERFACE"
            ;;
        *)
            echo "Changing to specified MAC address: $OPTION"
            sudo macchanger -m "$OPTION" "$INTERFACE"
            ;;
    esac
    
    echo "Bringing up interface $INTERFACE..."
    sudo ip link set dev "$INTERFACE" up
    
    echo "Current MAC address:"
    macchanger -s "$INTERFACE"
}

check_interface
change_mac
```

network_reset.sh
```bash
#!/bin/bash

INTERFACE=${1:-eth0}

echo "Resetting network interface: $INTERFACE"

sudo ip link set dev "$INTERFACE" down
sudo macchanger -p "$INTERFACE"
sudo systemctl restart NetworkManager
sudo ip link set dev "$INTERFACE" up

echo "Network interface $INTERFACE has been reset"
```

Troubleshooting

Common Issues

1. Interface not found
   ```bash
   ip link show
   ifconfig -a
   ```

2. Permission denied
   ```bash
   sudo macchanger -r eth0
   ```

3. Changes do not persist after reboot
   ```bash
   # MAC changes are temporary by default
   # To make permanent, add to startup scripts
   ```

4. Network connection lost
   ```bash
   sudo systemctl restart NetworkManager
   sudo ifconfig eth0 down && sudo ifconfig eth0 up
   ```

Verification Commands

```bash
ip link show eth0 | grep ether
macchanger -s eth0
ifconfig eth0
```

Best Practices

1. Always backup original MAC address
   ```bash
   macchanger -s eth0 > original_mac.txt
   ```

2. Test connectivity after changes
   ```bash
   ping -c 4 8.8.8.8
   ```

3. Use appropriate MAC addresses
   - Avoid broadcast addresses FF:FF:FF:FF:FF:FF
   - Use locally administered addresses when possible
   - Research vendor OUIs for realistic spoofing

4. Monitor network behavior
   ```bash
   nmcli device status
   ```

Additional Tools

Useful Commands
```bash
openssl rand -hex 6 | sed 's/\(..\)/\1:/g; s/.$//'
macchanger -l | head -20
macchanger -s eth0
```

Network Monitoring
```bash
watch -n 1 'cat /proc/net/dev'
arp -a
sudo tcpdump -i eth0 -n
```

Legal Disclaimer

IMPORTANT This tool is intended for educational purposes, authorized penetration testing, and legitimate network administration only.

- Only use on networks you own or have explicit permission to test
- Respect all applicable laws and regulations
- Unauthorized network access is illegal in most jurisdictions
- The authors are not responsible for misuse of this information

Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

License

This project is licensed under the MIT License - see the LICENSE file for details.

Support

For issues and questions:
- Open an issue on GitHub
- Check Kali Linux documentation
- Consult network administration resources
