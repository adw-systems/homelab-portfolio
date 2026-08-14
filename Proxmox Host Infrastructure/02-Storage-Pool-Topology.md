### Proxmox VE Storage Architecture & Disk Topology Blueprint

This architecture runbook details the storage sub-system layout, disk wear analytics, and ZFS partition schemas supporting the virtualization infrastructure. 

### 1. Storage Inventory & Hardware Matrix

|Device Identifier|Hardware Interface|Disk Model / Vendor|Raw Capacity|Current Wearout Metric|Architectural Role Allocation|
|---|---|---|---|---|---|
|`/dev/nvme0n1`|NVMe PCIe SSD|KIOXIA KBG40ZNS512G|512.11 GB|**14% Wearout**|Core Hypervisor Root Operating System (ZFS Pool)|
|`/dev/nvme1n1`|NVMe PCIe SSD|KIOXIA KBG40ZNS512G|512.11 GB|**4% Wearout**|High-Speed Compute Virtual Disk Pool / LXC Storage|
|`/dev/nvme2n1`|NVMe PCIe SSD|KIOXIA KXG60ZNV512G|512.11 GB|**0% Wearout**|Target Isolation Node for Local LLM Core Inference|
|`/dev/sda`|SATA III SSD|Crucial CT2000MX500SSD1|2.00 TB|**16% Wearout**|Secondary High-Capacity Application Database Pool|
|`/dev/sdb`|SATA III SSD|Crucial CT2000MX500SSD1|2.00 TB|**13% Wearout**|Secondary High-Capacity Application Database Pool|
|`/dev/sdc`|Mechanical HDD|WD Purple WD141PURP|14.00 TB|**0% (N/A)**|Raw Backing Block Device for Proxmox Backup Files|

### 2. Partition Formatting & File System Topology

- **NVMe Array Provisioning Schema:** The high-speed flash layer is segmented using standard GUID Partition Tables (GPT). Every flash drive allocates exactly `510.03 GB` to a dedicated `ZFS` storage pool block to leverage enterprise data integrity hashing. 
    
- **Solid-State Drive (SSD) Pooling:** The Crucial SATA SSDs (`/dev/sda` and `/dev/sdb`) are formatted completely with enterprise `ZFS` and `ZFS reserved` partitions, ready to scale out block volumes across a large data footprint. 
    
- **Mass Storage Backup Targets:** The `/dev/sdc` 14.00 TB hard disk drive contains no operational partition data layers. This unallocated raw disk space is reserved to receive automated hypervisor virtual node snapshot dumps (`vzdump`).