### Proxmox VE Hypervisor Host Hardening Runbook

This runbook documents the security configurations applied directly to the bare-metal hypervisor host layer to protect administrative tracking interfaces, eliminate password-based vectors, and enforce strict network isolation. 

---

### 1. Identity & Access Management (IAM) Isolation

To align with enterprise security baselines, daily administrative actions are decoupled from the root user via Linux Pluggable Authentication Modules (PAM) and Proxmox Access Control Lists (ACLs). 

### Linux Account Provisioning & Privilege Elevation

1. Initialize a new administrative operations user profile within the underlying Debian host environment. Replace `<username>` with your chosen non-generic identifier:
    
```
su -
adduser <username>
```
    
2. Install the missing `sudo` management utility framework:
    
```
apt update && apt install sudo -y
```
    
3. Append the new account string to the local `sudo` system group to authorize root elevation rights:
    
```
usermod -aG sudo <username>
```
   

### Proxmox System RBAC Mapping

Register the underlying PAM identity profile inside the hypervisor application realm and enforce global Administrator directory inheritance rules using the core user management engine:

```
# Add user string into the Linux PAM authentication realm
pveum user add <username>@pam

# Map the global administrative role across the system directory root (/)
pveum acl modify / --roles Administrator --user <username>@pam
```


---

### 2. Cryptographic-Only SSH Boundary Protection

Administrative access to the host terminal requires modern, un-breakable cryptographic key pairs. Password authentication and root logins are completely disabled.

### Configuration Adjustment Matrix

1. Generate a secure modern key pair inside your local client machine's shell environment (e.g., Windows PowerShell or macOS Terminal):
    
```
ssh-keygen -t ed25519 -C "admin-client-key"
```
    
2. Push the public key payload (`id_ed25519.pub`) onto a single line inside the newly provisioned host user directory profile:
    
```
# Run these commands as your newly created user profile
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
# Paste your public key string inside the file, save and close.
chmod 600 ~/.ssh/authorized_keys
```
    
3. Open the host SSH endpoint configuration descriptor:
    
```
sudo nano /etc/ssh/sshd_config
```
    
4. Enforce user separation and drop password authentication fallbacks by applying these parameters:
    
```
# Deny direct root administrative sessions over SSH
PermitRootLogin no

# Force strict cryptographic key verification
PubkeyAuthentication yes

# Explicitly reject password-based login attempts across the daemon
PasswordAuthentication no
PermitEmptyPasswords no
```
    
5. **CRITICAL SAFETY STEP:** Open a separate backup terminal window before committing changes. If a syntax mistake is made, your active session remains connected to fix it. Restart the networking daemon:
    
```
sudo systemctl restart sshd
```
    
    

---

### 3. Network Isolation & Tailscale Overlay Mesh

To prevent exposure to public interfaces or compromised entities on the local area network (LAN), the Proxmox Web UI and SSH engines are tightly bound to an encrypted Tailscale overlay interface.

### Tailscale Deployment

1. Initialize the official Tailscale automated deployment architecture script directly onto the host terminal:
    
```
sudo curl -fsSL https://tailscale.com/install.sh | sh
```
    
2. Connect and authenticate the bare-metal node into your private Tailnet mesh network:
    
```
sudo tailscale up
```
    

---

### 4. Hypervisor Maintenance & Infrastructure Hardening

### Repository Correction (No-Subscription Streams)

Switch default enterprise licencing pathways to the stable community open-source definitions to maintain regular package updates:

```
# Disable the enterprise list file tracking hooks
sudo sed -i 's/^deb/#deb/' /etc/apt/sources.list.d/pve-enterprise.list

# Append the stable non-production open source repository mirror
echo "deb http://proxmox.com bookworm pve-no-subscription" | sudo tee /etc/apt/sources.list.d/pve-no-subscription.list
```

### Unattended Security Upgrades

Automate the background deployment of critical Linux operating system security patches:

```
sudo apt install unattended-upgrades unattended-upgrades-blacklist -y
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

### Management Panel Verification Layer

1. Access your Proxmox Web GUI over your secure Tailscale IP (`https://100.x.y.z:8006`).
2. Navigate to **Datacenter** > **Two-Factor Authentication**.
3. Select **Add** and enforce a strict **TOTP** (Time-Based One-Time Password) challenge requirement across your administrative profile.
