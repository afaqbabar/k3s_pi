K3s and FluxCD Setup on Raspberry Pi 4
This repository documents the process of setting up Kubernetes (using k3s) and GitOps (using FluxCD) on a Raspberry Pi 4.

Prerequisites
Raspberry Pi 4

Raspberry Pi OS (64-bit recommended)

GitHub account and personal access token

Installation Steps
1. Fix Memory Cgroup Issue
Edit the boot configuration to enable memory cgroups:

bash
sudo nano /boot/cmdline.txt
Add the following parameters to the end of the existing line (don't create a new line):

text
cgroup_memory=1 cgroup_enable=memory
2. Install iptables Tools
Install necessary networking tools:

bash
sudo apt update
sudo apt install -y iptables-persistent
3. Reboot and Install K3s
Reboot to apply cgroup changes:

bash
sudo reboot
After reboot, install k3s:

bash
curl -sfL https://get.k3s.io | sh -
Verify installation:

bash
sudo systemctl status k3s
sudo kubectl get nodes
4. Set Up FluxCD
Configure kubectl access:

bash
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $USER:$USER ~/.kube/config
Install FluxCD:

bash
curl -s https://fluxcd.io/install.sh | sudo bash
flux check --pre
5. Bootstrap FluxCD with GitHub
Set your GitHub token:

bash
export GITHUB_TOKEN=<Your_GITHUB_TOKEN>
Bootstrap FluxCD (replace with your details):

bash
flux bootstrap github \
  --owner=your_github_username \
  --repository=your_repository_name \
  --branch=main \
  --path=./clusters/pi-cluster
Verification
Check FluxCD components:

bash
sudo kubectl get pods -n flux-system
Check kustomizations:

bash
flux get kustomizations
Example Deployment
The repository includes an example nginx deployment that demonstrates the GitOps workflow in action.

Troubleshooting
If you encounter cgroup issues, ensure you've properly edited /boot/cmdline.txt and rebooted

For networking issues, verify iptables is properly installed

Check FluxCD logs for any synchronization issues: kubectl logs -n flux-system -l app=source-controller

Resources
K3s Documentation

FluxCD Documentation

Raspberry Pi Kubernetes Cluster

License
This project is licensed under the MIT License.
