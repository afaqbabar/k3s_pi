# Install k3s on Raspberry pi 4

# 1. Fix Memory Cgroup Issue
# Edit the boot configuration
```bash
sudo nano /boot/cmdline.txt
```

# Add this to the end of the existing line (don't create a new line):
```bash
cgroup_memory=1 cgroup_enable=memory
```

# The line should look something like:
# console=serial0,115200 console=tty1 root=PARTUUID=... cgroup_memory=1 cgroup_enable=memory

# 2. Install Missing iptables Tools
# Install iptables-persistent
```bash
sudo apt update
sudo apt install -y iptables-persistent
```

# Or if you prefer lighter weight:
```bash
sudo apt install -y iptables
```

# 3. Reboot and Reinstall
# Reboot to apply cgroup changes
```bash
sudo reboot
```

# After reboot, uninstall and reinstall k3s
```bash
sudo /usr/local/bin/k3s-uninstall.sh
```

# Reinstall k3s
```bash
curl -sfL https://get.k3s.io | sh -
```
```bash
# Check status
sudo systemctl status k3s
sudo kubectl get nodes
```

