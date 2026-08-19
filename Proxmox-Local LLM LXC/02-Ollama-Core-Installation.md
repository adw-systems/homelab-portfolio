### Service Provisioning: Native Ollama Inference Engine & CPU Core Optimization

This runbook documents the deployment, service integration, and bare-metal processor thread optimization configurations for running local inference models on an Intel i5 CPU architecture. 

---

### 1. Prerequisite: Host-to-Container Hardware Interface Mapping

Because the application resides inside an unprivileged container environment, the underlying hypervisor must explicitly delegate rendering capabilities to the container's backend control blocks before initializing the service. 

### Configuration Insertion (`/etc/pve/lxc/100.conf`)

The primary resource allocation descriptor on the Proxmox bare-metal host shell was appended with the following hardware nodes to safely bridge the integrated graphics chip: 

text

```
# Allow container to access hardware graphics render node blocks (Major number 226)
lxc.cgroup2.devices.allow: c 226:* rwm

# Mount the physical device pathways directly into the container filesystem
lxc.mount.entry: /dev/dri/card1 dev/dri/card1 none bind,optional,create=file
lxc.mount.entry: /dev/dri/renderD128 dev/dri/renderD128 none bind,optional,create=file
```

Use code with caution.

---

### 2. Automated Binary Installation & Integration

### Package Ingestion

Because the foundational network gateway constraints were remediated, the automated installer script was retrieved over secure network sockets: 

bash

```
curl -fsSL https://ollama.com/install.sh | sh
```

Use code with caution.

The installer pipeline registers an isolated system execution service (`ollama.service`) running on default network loopback port `11434`. 

### Service Verification

Audit daemon operations to verify a stable runtime environment: 

bash

```
sudo systemctl status ollama
```

Use code with caution.

---

### 3. Low-Level Core Processing Optimization (AVX2 Thread Mapping)

To prevent severe latency penalties caused by system hyperthreading scheduling across virtual execution tracks, the runtime engine is pinned precisely to the host's physical processor core count. 

### Systemd Environment Overrides (`sudo systemctl edit ollama`)

The background service layer is injected with explicit parameter boundaries to maximize matrix computation speeds while completely locking down the local socket listener interface: 

text

```
[Service]
# Lock ollama to listen only to its local container
Environment="OLLAMA_HOST=127.0.0.1"

# Lock execution mapping exactly to the host machine's 6 physical compute cores
Environment="OLLAMA_NUM_THREADS=6"

# Constrain memory overhead to a single active text generation stream
Environment="OLLAMA_NUM_PARALLEL=1"
```

Use code with caution.

### Daemon Commit Lifecycle

Reload the hypervisor container control indexes and restart the application stack to bind your optimization parameters: 

bash

```
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

Use code with caution.