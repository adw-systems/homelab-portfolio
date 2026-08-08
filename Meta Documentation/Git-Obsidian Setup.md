### Developer Environment & Portfolio Workspace Setup

This document outlines the standard bootstrap procedure for configuring a secure, automated markdown technical documentation workflow using Obsidian and Git. 

---

### 1. System Installation & Core Setup

### Git Client Installation

1. Download the native client binary from [git-scm.com](https://git-scm.com/downloads). 
    
2. Execute the installer utility utilizing default system configuration presets. 
    
3. Validate operational initialization via shell environment: 
    
    bash
    
    ```
    git version
    ```
    
    Use code with caution.
    

### Global Identity Mapping

Set your system-wide commit metadata to align with your active enterprise profile: 

bash

```
git config --global user.name "Your Name"
git config --global user.email "your-github-email@example.com"
```

Use code with caution.

---

### 2. Asymmetric Cryptographic Authentication (SSH)

To eliminate plaintext credential risks over standard HTTPS network layers, use Secure Shell (SSH) keys. 

### Key Pair Generation

Generate a modern ED25519 key pair with high computational resistance: 

powershell

```
ssh-keygen -t ed25519 -C "your-github-email@example.com"
```

Use code with caution.

_Accept default paths by pressing `Enter` through all sequential prompt sequences._ 

### Profile Synchronization

1. Print the public half of the generated key map: 
    
    powershell
    
    ```
    Get-Content ~\.ssh\id_ed25519.pub
    ```
    
    Use code with caution.
    
2. Copy the explicit output block string (starting with `ssh-ed25519`). 
    
3. Access **GitHub -> Settings -> SSH and GPG keys -> New SSH Key**. 
    
4. Paste the complete public key text block, label the hardware profile, and save. 
    

---

### 3. Local Workspace Isolation & Security Filters

### Directory Creation

1. Install the desktop markdown engine from [Obsidian.md](https://obsidian.md/). 
    
2. Create an isolated directory structure (e.g., `homelab-portfolio`). Do not link to broad personal folders. 
    

### The Security Filter (`.gitignore`)

To prevent accidental pushes of system configurations or explicit server passwords, initialize a core directory block list. Run this directly from PowerShell inside your vault directory: 

powershell

```
Set-Content .gitignore ".obsidian/`n.DS_Store`n*.env`n*.secret`ncredentials.*"
```

Use code with caution.

---

### 4. Upstream Remote Synchronization Pipeline

Initialize tracking within your local workspace root, bind it to your cloud endpoint, and push the initial codebase: 

powershell

```
# Initialize local tracking index
git init

# Bind the target public upstream cloud location
git remote add origin git@github.com:your-username/homelab-portfolio.git

# Stage all files passing current security criteria
git add .

# Save a baseline point-in-time snapshot
git commit -m "docs: initial portfolio layout and security configurations"

# Rename working branch to corporate standard and push
git branch -M main
git push -u origin main
```

Use code with caution.