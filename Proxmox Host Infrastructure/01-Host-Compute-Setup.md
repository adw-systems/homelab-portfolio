### Bare-Metal Proxmox VE Hypervisor Host Setup

This documentation runbook records the initial architectural provisioning baseline for the central compute host supporting home lab infrastructure automation projects. 

### 1. Hardware Platform & Compute Allocation

### Compute Specifications

- **Host Hardware Platform:** Dell Precision 3450 Small Form Factor (SFF) Workstation 
    
- **Processor Architecture:** Intel Core i5-11500 (6 Physical Cores / 12 Processing Threads / AVX2 capable) 
    
- **System Volatile Memory (RAM):** 32 GB DDR4 Non-ECC Memory Matrix 
    
- **Aggregated Hardware Storage Capacity:** 3.54 Terabytes 
    

### Underlying Hardware Initialization (BIOS/UEFI Configuration)

To authorize bare-metal kernel control layers to handle execution scheduling, the following physical motherboard parameters are enabled: 

- **Intel Virtualization Technology (VT-x):** Mandated for core virtualization handling routines. 
    
- **Intel VT for Directed I/O (VT-d):** Enforced to secure low-latency I/O hardware device passthrough capabilities. 
    

### 2. Operating System & Hypervisor Configuration

### Core Deployment Matrix

- **Platform Software Stack:** Proxmox Virtual Environment (PVE) 
    
- **Cluster Topology State:** Standalone Node Deployment Profile (No active multi-node cluster binding) 
    
- **Active Host Identifier:** `pve` 
    

### Local Management Network Footprint Layout

The physical management console interfaces utilize static network binding profiles mapped within a sanitized private subnet configuration: 

- **Dedicated Node Gateway Target IP Address:** `10.0.0.10` 
    
- **Secure Administration GUI Access URI:** `https://10.0.0.10:8006/` 
    
- **Local Management Network Mask Target:** Subnet Scope `/24` (Netmask: `255.255.255.0`)

