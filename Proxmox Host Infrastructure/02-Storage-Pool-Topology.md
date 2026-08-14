### Proxmox VE Hardware Layout & Storage Architecture

This document outlines the production deployment topology, physical disk tiering strategy, and filesystem optimization matrix implemented on the bare-metal Proxmox VE hypervisor host. 

---

### 1. Storage Pool Architecture Matrix

The host utilizes three distinct storage tiers categorized by performance characteristics, physical bus interfaces, and underlying ZFS parity configurations. Mixing distinct drive classes within a single pool is explicitly avoided to prevent I/O bottlenecking. 

|Pool Name|Physical Drives|ZFS Layout|Usable Capacity|Target Workloads / Allocations|
|---|---|---|---|---|
|**`rpool`**|3x 512 GB NVMe|**RAIDZ1**|~1.0 TB|Proxmox OS, High-IOPS databases, production VMs|
|**`fast-storage`**|2x 2.0 TB SATA SSD|**Mirror (RAID1)**|~2.0 TB|Standard application VMs, Linux Containers (LXCs)|
|**`bulk-data`**|1x 14.0 TB SATA HDD|**Single Disk**|~14.0 TB|Local backups, templates, ISO images, cold storage|

---

### 2. Structural Deployment & Implementation

### Phase 1: Boot Layer Provisioning (Installer Configuration)

During the Proxmox VE initialization phase, the primary operating system environment is bound directly to the high-performance NVMe array: 

1. Target Harddisk configured via **Advanced Options**. 
    
2. Filesystem type set to `zfs (RAIDZ1)`. 
    
3. Array membership assigned explicitly to the three NVMe nodes. 
    
4. Sector alignment locked at default (`ashift=12`) for optimal solid-state sector mapping. 
    

### Phase 2: Secondary Tier Allocation (Post-Install CLI / Web UI)

Secondary storage frameworks are provisioned post-boot via node terminal access or the administration engine: 

bash

```
# Provision the secondary SSD tier as a resilient mirror array
# Web UI Path: Datacenter -> [Node] -> Disks -> ZFS -> Create: ZFS (Mirror)
# Assigned name: fast-storage

# Provision the capacity-tier mass storage layout
# Web UI Path: Datacenter -> [Node] -> Disks -> ZFS -> Create: ZFS (Single Disk)
# Assigned name: bulk-data
```

Use code with caution.

---

### 3. Write-Amplification Mitigation & Performance Tuning

To optimize disk endurance and eliminate synchronous write penalties common to consumer-grade solid-state media (lacking enterprise Power-Loss Protection hardware), transaction behaviors are tuned directly at the pool layout root: 

bash

```
# Disable rigid sync constraints on NVMe boot layout to accelerate VM transactional disk operations
zfs set sync=disabled rpool

# Mirror performance optimization applied to secondary solid-state storage framework
zfs set sync=disabled fast-storage
```

Use code with caution.

**Operational Warning:** Setting `sync=disabled` permits ZFS to buffer pending writes within the volatile system memory layer for up to 5 seconds. In the event of an unmitigated hardware power failure lacking an upstream Uninterruptible Power Supply (UPS), the last transaction window may experience loss. Ensure automated off-site dataset replication tasks are structured for state-critical workloads.