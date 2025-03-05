# Home Network Migration and Optimization Plan

## Overview

This document outlines the migration from a virtualized network infrastructure (ESXi + OPNsense) to a hardware-based solution using the Ubiquiti Dream Machine Pro Max (UDMP Max). The plan includes network simplification, security hardening, and performance optimizations.

## Network Hardware

### Core Components
- **Security Gateway**: Ubiquiti Dream Machine Pro Max
- **Core Switch**: Mikrotik CRS504-4XQ
- **Distribution Switches**:
  - Mikrotik CRS312-4C-8XG
  - QNAP QSW-M2116P-2T2S
  - Ubiquiti USW-PRO-24
  - Mokerlink 8 x 2.5G Ethernet + 10G SFP Switches (2x)
- **Wireless**: Ubiquiti U6-ENTERPRISE-US (3x)

### WAN Connectivity
- **Primary**: Verizon FiOS 2G Symmetric (planned)
- **Current**: Comcast Xfinity

## Simplified VLAN Structure

| VLAN ID | Network CIDR    | Purpose/Description                   |
|---------|-----------------|--------------------------------------|
| 1       | 192.168.1.0/24  | General Home Network (WiFi, devices) |
| 10      | 10.10.0.0/24    | Management Network (network devices) |
| 20      | 10.20.0.0/24    | Server Network (VM workloads)        |
| 30      | 10.30.0.0/24    | IoT Network (smart home devices)     |
| 40      | 10.40.0.0/24    | Guest Network (visitors)             |

### VLAN Design Principles
- **Functional Segmentation**: Networks segregated by function and security requirements
- **Consistent IP Schemes**: 10.x.0.0/24 blocks for infrastructure
- **Intuitive Numbering**: Simple VLAN IDs for easier management

## Physical Connectivity

### High-Level Design
```
Internet → UDMP Max → Mikrotik CRS504-4XQ (Core) → Distribution Switches → End Devices
```

### Connection Details
- UDMP Max to CRS504-4XQ: 10G SFP+ connection
- CRS504-4XQ to Distribution Switches: 10G fiber connections
- Distribution to Access: Mix of 10G and 1G depending on device requirements

## Security Configuration

### Inter-VLAN Firewall Rules

| Source VLAN | Destination VLAN | Access                               |
|-------------|------------------|--------------------------------------|
| 1 (General) | 10 (Management)  | Limited (HTTP/HTTPS to specific IPs) |
| 1 (General) | 20 (Server)      | Allowed (HTTP/HTTPS, SMB, Media)     |
| 1 (General) | 30 (IoT)         | Limited (Control services only)      |
| 10 (Mgmt)   | All              | Allowed (Management requires access) |
| 20 (Server) | 1, 10            | Limited (Defined services)           |
| 20 (Server) | 30, 40           | Blocked                              |
| 30 (IoT)    | Internet         | Allowed (Restricted ports)           |
| 30 (IoT)    | All Internal     | Blocked (Except specific services)   |
| 40 (Guest)  | Internet         | Allowed                              |
| 40 (Guest)  | All Internal     | Blocked                              |

### Security Hardening
- IDS/IPS enabled in balanced mode
- DNS filtering for malware domains
- DPI for traffic analysis
- IoT device isolation
- Guest network client isolation

## Optimization Configurations

### DNS and DHCP
- UDMP Max as primary DNS server with local records
- DHCP Scopes:
  - General: 192.168.1.100-192.168.1.250 (1 day lease)
  - Management: 10.10.0.100-10.10.0.200 (7 day lease)
  - Server: 10.20.0.100-10.20.0.200 (7 day lease)
  - IoT: 10.30.0.100-10.30.0.250 (3 day lease)
  - Guest: 10.40.0.100-10.40.0.250 (8 hour lease)
- IP reservations for all critical infrastructure

### WiFi Configuration
- Primary SSID: Connected to VLAN 1
- IoT SSID: Connected to VLAN 30
- Guest SSID: Connected to VLAN 40
- WPA3 enabled where supported
- Band steering enabled
- Minimum RSSI requirements

### QoS Settings
- Video conferencing traffic priority: High
- VoIP traffic priority: Highest
- Streaming media: Medium
- Bulk downloads: Low
- Guest traffic: Rate limited to 50Mbps per client

### High Availability
- Dual PSU where available
- Critical equipment on UPS backup
- Configuration backups stored off-network

## Virtualization Integration

### Proxmox Implementation
- Proxmox to replace ESXi hosts
- VM networks connected to appropriate VLANs
- Storage network isolated on dedicated VLAN if needed
- Backup strategy implemented for VMs

## Migration Strategy

### Phase 1: UDMP Max Setup (Day 1)
- Configure UDMP Max with new VLAN structure
- Connect to core switch without WAN
- Test internal routing

### Phase 2: Gradual Migration (Days 2-3)
- Migrate management devices to VLAN 10
- Move servers to VLAN 20
- Relocate IoT devices to VLAN 30
- Configure Guest network on VLAN 40

### Phase 3: WAN Cutover (Day 4)
- Schedule maintenance window
- Connect UDMP Max to WAN
- Test all connectivity
- Verify firewall rules

### Phase 4: Optimization (Week 2)
- Fine-tune IDS/IPS settings
- Implement QoS rules
- Configure monitoring and alerts
- Document final configuration

## Monitoring & Maintenance

### Regular Tasks
- Weekly configuration backups
- Monthly firmware updates (after testing)
- Quarterly security rule review
- Security log review schedule

### Monitoring Points
- WAN connectivity and performance
- Inter-VLAN routing performance
- IDS/IPS alerts
- Unusual traffic patterns
- Device connectivity

## Future Expansion

### Short-term Plans
- Transition to Verizon FiOS 2G
- Full Proxmox implementation
- Expanded home automation

### Long-term Considerations
- IPv6 implementation
- Potential 25G core upgrade path
- Additional wireless coverage

## Disaster Recovery

### Recovery Documentation
- UDMP Max configuration restore procedure
- Network bootstrap process
- Prioritized service restoration list
- Offline copies of all critical configurations
- Recovery time objectives for key services

---

*This document should be updated as changes are made to the network infrastructure.*

Last Updated: [Current Date]
