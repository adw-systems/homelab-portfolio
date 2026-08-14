### Service Deployment: Advanced Docker Engine & Open WebUI Frontend Integration

This runbook documents the manual package repository pinning, user group access elevations, and container deployment procedures executing an enterprise web application frontend interface. 

---

### 1. Manual Package Pining & APT GPG Verification

### Advanced Repository Configuration

To guarantee system stability and package authenticity, Docker’s official GNU Privacy Guard (GPG) cryptographic keys were integrated manually into the system keyring: 

bash

```
# Register security certificates and establish directory targets
sudo apt update
sudo apt install ca-certificates curl -y
sudo install -m 0755 -d /etc/apt/keyrings

# Ingest and apply read permissions across the verified public key binary
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Use code with caution.

### Upstream Sources Integration

Inject the official, stable distribution pathway architecture mapping directly to the underlying Ubuntu codename parameters: 

bash

```
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: \$(. /etc/os-release && echo "\${UBUNTU_CODENAME:-\$VERSION_CODENAME}")
Components: stable
Architectures: \$(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

Use code with caution.

### Core Engine Installation

Unpack the enterprise virtualization binaries, container runtime socket abstraction interfaces, and plugin architectures: 

bash

```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

Use code with caution.

---

### 2. Containerized Application Orchestration & Ingress Bridging

### Socket Permission Elevation

To manage active tasks securely over network boundaries without maintaining direct root terminal shell states, the administrative profile is assigned explicitly to the container runtime socket tracking arrays: 

bash

```
sudo usermod -aG docker sysadmin
```

Use code with caution.

### Infrastructure Deployment Pipeline

The application stack uses low-latency host networking overlays to bridge communication paths cleanly straight to the underlying native engine logic backend processing on port `11434`: 

bash

```
sudo docker run -d \
  --network=host \
  --name open-webui \
  --restart always \
  -v open-webui:/app/backend/data \
  ghcr.io/open-webui/open-webui:main
```

Use code with caution.

### Architectural Parameter Definitions

- `--network=host`: Directs the application to map straight into the primary network stack, resolving inner loopback connectivity requirements to port `11434`. 
    
- `-v open-webui:/app/backend/data`: Binds an isolated volume checkpoint directory to ensure internal user profiles and chat history states persist across service reboots. 
    
- `--restart always`: Enforces container execution state restoration policies if the hosting hypervisor experiences hard restarts or service drops.