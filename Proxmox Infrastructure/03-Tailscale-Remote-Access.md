### Secure Remote Ingress Architecture via Tailscale Overlay Mesh Network

This runbook documents the configuration parameters for securely administering the hypervisor environment remotely without using port-forwarding vectors on the border firewall. 

### 1. Network Topography & Overlay Configuration

### The Mesh Network (Tailscale VPN)

To allow encrypted, peer-to-peer remote infrastructure management, the Proxmox host is provisioned as an active node within an isolated WireGuard-based Tailscale overlay network: 

- **Overlay Network Layer:** Tailscale Access Control List (ACL) Restricted Zone 
    
- **Host Overlay Identifier:** Fully Qualified Domain Name (FQFN) assigned via Tailscale MagicDNS (`pve.your-tailnet.ts.net`). 
    
- **Split-Tunnel Routing:** Secure connectivity is constrained to authenticated mesh nodes, bypassing traditional edge-router firewall rules. 
    

### 2. Public Key Infrastructure (PKI) & TLS Certificate Provisioning

### Security Certificate Deployment

To eliminate browser safety warnings (`NET::ERR_CERT_AUTHORITY_INVALID`) and encrypt all administrative payload traffic inside the mesh network, a valid TLS security certificate is provisioned: 

- **Certificate Authority (CA):** Let's Encrypt automated production signing infrastructure. 
    
- **Validation Protocol:** Automated Acme client handshake mapping to the Tailscale machine domain name using native Tailscale TLS issuance integrations (`tailscale cert`). 
    
- **Cryptographic Layer:** 256-bit AES transport layer encryption enforced globally across the hypervisor web portal on port `8006`.