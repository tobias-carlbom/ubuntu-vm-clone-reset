#!/bin/bash

# Script to reset identifiers on a cloned Ubuntu VM
# Run this with sudo after cloning a VM from a VHDX file

set -e  # Exit on error

echo "================================================"
echo "Ubuntu Cloned VM Reset Script"
echo "================================================"
echo ""

# Check if running as root
if [ "$EUID" -ne 0 ]; then 
    echo "ERROR: This script must be run as root (use sudo)"
    exit 1
fi

# Get new hostname from user
read -p "Enter new hostname for this VM: " NEW_HOSTNAME

if [ -z "$NEW_HOSTNAME" ]; then
    echo "ERROR: Hostname cannot be empty"
    exit 1
fi

echo ""
echo "This script will:"
echo "  1. Reset machine-id"
echo "  2. Regenerate SSH host keys"
echo "  3. Set hostname to: $NEW_HOSTNAME"
echo "  4. Update /etc/hosts"
echo ""
read -p "Continue? (y/n): " -n 1 -r
echo ""

if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    echo "Aborted."
    exit 0
fi

echo ""
echo "[1/4] Resetting machine-id..."
rm -f /etc/machine-id /var/lib/dbus/machine-id
systemd-machine-id-setup
echo "✓ Machine ID reset"

echo ""
echo "[2/4] Regenerating SSH host keys..."
rm -f /etc/ssh/ssh_host_*
dpkg-reconfigure -f noninteractive openssh-server
echo "✓ SSH host keys regenerated"

echo ""
echo "[3/4] Setting hostname to $NEW_HOSTNAME..."
hostnamectl set-hostname "$NEW_HOSTNAME"
echo "✓ Hostname set"

echo ""
echo "[4/4] Updating /etc/hosts..."
sed -i "s/127.0.1.1.*/127.0.1.1\t$NEW_HOSTNAME/" /etc/hosts
echo "✓ /etc/hosts updated"

echo ""
echo "================================================"
echo "✓ VM reset complete!"
echo "================================================"
echo ""
echo "IMPORTANT: Please reboot the VM now for all changes to take effect."
echo ""
read -p "Reboot now? (y/n): " -n 1 -r
echo ""

if [[ $REPLY =~ ^[Yy]$ ]]; then
    echo "Rebooting in 3 seconds..."
    sleep 3
    reboot
else
    echo "Remember to reboot manually: sudo reboot"
fi
