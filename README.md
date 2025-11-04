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
wget https://raw.githubusercontent.com/tobias-carlbom/ubuntu-vm-clone-reset/main/reset-cloned-vm.sh
```

2. Make it executable:
```bash
chmod +x reset-cloned-vm.sh
```

3. Run with sudo:
```bash
sudo ./reset-cloned-vm.sh
```

4. Follow the prompts to enter a new hostname

5. Reboot when complete

## Requirements

- Ubuntu Server 24.04 (should work on other Ubuntu versions too)
- Root/sudo access

## Note

This is the Linux equivalent of running `sysprep` on Windows VMs after cloning. It's much simpler because Linux doesn't have hardware-specific activation or complex licensing tied to system identifiers.

## License

MIT
