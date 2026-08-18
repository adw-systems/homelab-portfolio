### Proxmox Host Hardening and Security Documentation

This guide outlines the security baseline implemented to harden the Proxmox VE (PVE) host against unauthorized access. Management access is strictly limited to trusted local networks and individual, authenticated client addresses via an encrypted mesh VPN (Tailscale). 

---

### 1. Web UI Management Two-Factor Authentication (2FA)

To mitigate credential stuffing or compromised passwords, Time-based One-Time Passwords (TOTP) are strictly enforced at the application tier for administrative realms. 

### Implementation Steps

1. Logged into the Proxmox Web UI as an administrative user. 
    
2. Navigated to **Datacenter** -> **Users**. 
    
3. Selected the target administrative account (e.g., `root@pam`) and clicked **Two-Factor Authentication**. 
    
4. Selected **TOTP** from the available mechanisms. 
    
5. Scanned the generated QR code using a secure authenticator application and saved the emergency fallback recovery keys in an offline password manager. 
    

---

### 2. Network Isolation via Proxmox Native Firewall

Network access to administrative management ports (SSH `22`, Web UI `8006`) is governed by an explicit drop-by-default inbound policy using a granular, least-privilege whitelist topology. 

### Step 1: Trusted Network Definition (IP Set)

A reusable IP set named `trusted_subnets` was created under **Datacenter** -> **Firewall** -> **IP Set** to aggregate authorized ingress origins. 

_In alignment with security best practices, the global Tailscale range (`100.64.0.0/10`) was omitted to minimize the blast radius of a compromised node. Only explicit management IPs are whitelisted:_ 

- **Management VLAN Subnet:** `YOUR_LOCAL_VLAN_SUBNET/CIDR` _(e.g., 192.168.1.0/24)_ 
    
- **Admin Client 1 (Tailscale):** `EXPLICIT_TAILSCALE_IP_1` _(e.g., 100.11.22.33)_ 
    
- **Admin Client 2 (Tailscale):** `EXPLICIT_TAILSCALE_IP_2` _(e.g., 100.44.55.66)_ 
    

### Step 2: Access Control Lists (Firewall Rules)

Inbound traffic rules were created under **Datacenter** -> **Firewall** -> **Rules** targeting only the defined IP set: 

|Direction|Action|Protocol|Dest. Port|Source Access Group|Comment|
|---|---|---|---|---|---|
|**`in`**|`ACCEPT`|`tcp`|`8006`|`+trusted_subnets`|Allow Web UI Access|
|**`in`**|`ACCEPT`|`tcp`|`22`|`+trusted_subnets`|Allow SSH Access|

### Step 3: Hardening Enforcements (Global Options)

The global firewall behavior was modified under **Datacenter** -> **Firewall** -> **Options** to transition from permissive routing to an explicit whitelist topology: 

- **Firewall:** `Yes` (Enables the PVE firewall orchestration engine) 
    
- **Input Policy:** `DROP` (Implicitly drops all inbound packets not explicitly matched by the whitelist rules above) 
    
- **Output Policy:** `ACCEPT` (Permits outbound updates and package distribution routing)