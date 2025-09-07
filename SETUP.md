# K3s Pi Cluster Setup

## Working Configuration

- K3s with built-in Traefik
- MetalLB for LoadBalancer services
- IP Range: 192.168.178.201-210

## Services

- Traefik: 192.168.178.201
- Nginx: Gets assigned IP from MetalLB pool

## Installation Commands Used

```bash
# Install MetalLB
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.12/config/manifests/metallb-native.yaml

# Configure IP pool
kubectl apply -f metallb-config.yaml

# Convert services to LoadBalancer type
kubectl patch svc nginx-service -p '{"spec":{"type":"LoadBalancer"}}'
