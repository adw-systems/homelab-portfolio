### Proxmox VE Hypervisor Host Hardening Runbook

This runbook documents the security configurations applied directly to the bare-metal hypervisor host layer to protect administrative tracking interfaces and enforce least-privilege principles. 

---

### 1. Identity & Access Management (IAM) Isolation

To align with enterprise security baselines, daily administrative and operational actions are decoupled from the root user via Linux Pluggable Authentication Modules (PAM) and Proxmox Access Control Lists (ACLs). 

### Linux Account Provisioning & Privilege Elevation

1. Initialize a new administrative operations user profile within the underlying Debian host environment using a sanitized generic identity: 
    
    bash
    
    ```
    adduser sysadmin
    ```
    
    Use code with caution.
    
2. Append the account string to the local `sudo` infrastructure array to authorize root elevation rights: 
    
    bash
    
    ```
    usermod -aG sudo sysadmin
    ```
   

### Proxmox System RBAC Mapping

Register the underlying PAM identity profile inside the hypervisor application realm and enforce global Administrator directory inheritance rules using the core user management engine: 

bash

```
# Add user string into the Linux PAM authentication realm
pveum user add sysadmin@pam

# Map the global administrative role across the system directory root (/)
pveum acl modify / --roles Administrator --user sysadmin@pam
```


---

### 2. Secure Shell (SSH) Boundary Protection

Because administrative traffic is securely tunneled internally through a private Tailscale overlay network, direct public or internal root SSH access points are blocked. Additionally, empty or null password strings are explicitly denied to reject unauthenticated access requests. 

### Configuration Adjustment Matrix

1. Open the host SSH endpoint configurations descriptor: 
    
    bash
    
    ```
    sudo nano /etc/ssh/sshd_config
    ```
    
    Use code with caution.
    
2. Set the following baseline tracking parameters to force user separation and password validation: 
    
    text
    
    ```
    # Deny direct root administrative sessions over SSH
    PermitRootLogin no
    
    # Explicitly reject connection requests from accounts with empty or null passwords
    PermitEmptyPasswords no
    ```
    
    Use code with caution.
    
3. Commit and apply structural security changes to the host networking daemon: 
    
    bash
    
    ```
    sudo systemctl restart sshd
    ```
    
    
    

---

### 3. Brute-Force Mitigation & Account Lockout Policies

To prevent automated credential-guessing vectors from exploiting open terminal pathways, the Linux Pluggable Authentication Module (PAM) subsystem is configured with a strict threshold lockout engine via `pam_faillock`. 

### Operational Parameters

- **Max Retry Threshold (`deny`):** 5 failed authentication attempts. 
    
- **Lockout Window (`unlock_time`):** 1800 seconds (30 minutes) cooling-off period. 
    

### Subsystem Implementation Rules

The parameters are injected directly into the core PAM authentication layers to audit and intercept bad login payloads: 

text

```
# Configuration added to /etc/pam.d/common-auth
auth required pam_faillock.so preauth silent audit deny=5 unlock_time=1800
auth [default=die] pam_faillock.so authfail audit deny=5 unlock_time=1800

# Configuration added to /etc/pam.d/common-account
account required pam_faillock.so
```


---
