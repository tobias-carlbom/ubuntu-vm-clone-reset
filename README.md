# Ubuntu VM Clone Reset

A simple script to reset system identifiers after cloning an Ubuntu VM from a VHDX/disk image.

## Why?

When you clone a VM by copying its disk file, the new VM retains the same machine ID, SSH host keys, and hostname as the original. This causes problems with system logging, SSH security, and network identification.

This script fixes that by resetting all the necessary identifiers.

## What it does

- Resets the machine-id (used by systemd and various services)
- Regenerates SSH host keys (security best practice)
- Sets a new hostname
- Updates `/etc/hosts` accordingly

## Usage

1. Clone this repo or download the script to your newly cloned VM:
```bash
wget https://raw.githubusercontent.com/tobias-carlbom/ubuntu-vm-clone-reset/main/reset-ubuntu-vm.sh
```

2. Make it executable:
```bash
chmod +x reset-ubuntu-vm.sh
```

3. Run with sudo:
```bash
sudo ./reset-ubuntu-vm.sh
```

4. Follow the prompts to enter a new hostname

5. Reboot when complete

## Requirements

- Ubuntu Server 24.04 (should work on other Ubuntu versions too)
- Root/sudo access

## Important Notes

### Setting a Static IP Address

After cloning, you'll likely want to assign a different IP address to avoid conflicts. Ubuntu Server uses Netplan for network configuration.

1. Edit the Netplan configuration:
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

2. Update the IP address (example configuration):
```yaml
network:
  ethernets:
    eth0:
      addresses:
        - 192.168.1.101/24  # Change this to your desired IP
      routes:
        - to: default
          via: 192.168.1.1   # Your gateway
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
  version: 2
```

3. Apply the changes:
```bash
sudo netplan apply
```

### SSH Known Hosts Warning on Client Machines

After the script regenerates SSH host keys, any machine that previously connected to the original VM will show a **"WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED"** error when trying to connect.

To fix this on your client machines, remove the old SSH key from `known_hosts`:

```bash
ssh-keygen -R <ip_address>
# or if using hostname
ssh-keygen -R <hostname>
```

**Example:**
```bash
ssh-keygen -R 192.168.1.101
```

Then connect normally - you'll be prompted to accept the new host key on first connection.

## Note

This is the Linux equivalent of running `sysprep` on Windows VMs after cloning. It's much simpler because Linux doesn't have hardware-specific activation or complex licensing tied to system identifiers.

## License

MIT
