# K3s and FluxCD Setup on Raspberry Pi 4

This repository documents the process of setting up Kubernetes (using k3s) and GitOps (using FluxCD) on a Raspberry Pi 4.

## Prerequisites

- Raspberry Pi 4
- Raspberry Pi OS (64-bit recommended)
- GitHub account and personal access token

## Installation Steps

### 1. Fix Memory Cgroup Issue

Edit the boot configuration to enable memory cgroups:

```bash
sudo nano /boot/cmdline.txt

Add the following parameters to the end of the existing line (don't create a new line):
cgroup_memory=1 cgroup_enable=memory
