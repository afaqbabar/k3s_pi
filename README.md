# K3s Pi Cluster with FluxCD

A GitOps-enabled Kubernetes cluster running on Raspberry Pi 4 using k3s and FluxCD for automated deployments.

## 🏗️ Architecture

- **Platform**: Raspberry Pi 4
- **Kubernetes**: k3s (lightweight Kubernetes distribution)
- **GitOps**: FluxCD v2.6.4
- **Repository**: GitHub-based GitOps workflow

## 🚀 Quick Start

### Prerequisites

- Raspberry Pi 4 with Raspberry Pi OS
- Internet connection
- GitHub account and personal access token
- Basic knowledge of Kubernetes and Git

### Installation Steps

#### 1. Fix Memory Cgroup Issue

k3s requires memory cgroups to be enabled on Raspberry Pi:

```bash
sudo nano /boot/cmdline.txt
```

Add the following to the end of the existing line (don't create a new line):

```bash
cgroup_memory=1 cgroup_enable=memory
```

The complete line should look like:
```bash
console=serial0,115200 console=tty1 root=PARTUUID=... cgroup_memory=1 cgroup_enable=memory
```

#### 2. Install Required Dependencies

Install iptables tools:

```bash
sudo apt update
sudo apt install -y iptables-persistent
```

#### 3. Install k3s

Reboot to apply cgroup changes:

```bash
sudo reboot
```

Install k3s:

```bash
curl -sfL https://get.k3s.io | sh -
```

Verify installation:

```bash
sudo systemctl status k3s
sudo kubectl get nodes
```

#### 4. Configure kubectl

Set up kubectl for your user:

```bash
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $USER:$USER ~/.kube/config
```

#### 5. Install FluxCD

Install Flux CLI:

```bash
curl -s https://fluxcd.io/install.sh | sudo bash
```

Verify prerequisites:

```bash
flux check --pre
```

#### 6. Bootstrap FluxCD with GitHub

Set your GitHub token:

```bash
export GITHUB_TOKEN=<Your_GitHub_Token>
```

Bootstrap Flux (replace with your GitHub username):

```bash
flux bootstrap github \
  --owner=afaqbabar \
  --repository=k3s_pi \
  --branch=main \
  --path=./clusters/pi-cluster
```

## 📁 Repository Structure

```
k3s_pi/
├── clusters/
│   └── pi-cluster/
│       ├── flux-system/
│       └── apps/
├── apps/
│   └── nginx/
│       ├── deployment.yaml
│       └── service.yaml
└── README.md
```

## 🔍 Verification Commands

Check Flux system status:

```bash
# Check Flux pods
sudo kubectl get pods -n flux-system

# Check Flux kustomizations
flux get kustomizations

# Check deployed applications
sudo kubectl get pods -n default
sudo kubectl get svc -n default
```

Expected output shows:
- All flux-system pods in Running state
- flux-system kustomization showing "Applied revision"
- Sample nginx deployment running with 2 replicas

## 🛠️ Troubleshooting

### Common Issues

**k3s installation fails:**
- Ensure memory cgroups are enabled and system is rebooted
- Check if iptables is properly installed

**Flux bootstrap fails:**
- Verify GitHub token has proper permissions (repo access)
- Ensure GitHub repository exists and is accessible

**kubectl permission denied:**
- Make sure kubeconfig is properly copied and ownership is set

### Useful Commands

```bash
# Restart k3s service
sudo systemctl restart k3s

# Check k3s logs
sudo journalctl -u k3s -f

# Uninstall k3s (if needed)
sudo /usr/local/bin/k3s-uninstall.sh

# Force Flux reconciliation
flux reconcile source git flux-system
flux reconcile kustomization flux-system
```

## 📦 Deployed Applications

Currently deployed:
- **nginx-deployment**: 2 replicas running on ClusterIP service
- **nginx-service**: Accessible internally on port 80

## 🔄 GitOps Workflow

1. Make changes to YAML files in this repository
2. Commit and push to the main branch
3. FluxCD automatically detects changes
4. Applications are deployed/updated in the cluster

## 🎯 Next Steps

- Add more applications to the `apps/` directory
- Set up monitoring with Prometheus/Grafana
- Configure ingress controller for external access
- Implement proper RBAC and security policies

## 📝 Notes

- Flux version: 2.6.4
- Cluster path: `./clusters/pi-cluster`
- GitHub repository: `afaqbabar/k3s_pi`
- Deploy key configured for automated Git operations

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

FluxCD will automatically sync approved changes to the cluster.

---

**Happy GitOps!** 🚀
