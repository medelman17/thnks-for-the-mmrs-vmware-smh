# Technitium DNS to UDMP Max Migration: Configuration Extraction Checklist

## Server Information
- [ ] Technitium DNS Server version
- [ ] Operating system and environment details (ESXi VM specifications)
- [ ] Server uptime and performance metrics
- [ ] CPU, memory, and disk utilization baselines
- [ ] IP address and network interface configuration
- [ ] Listening ports and interfaces

## Core DNS Configuration
- [ ] DNS server settings
  - [ ] Protocol settings (UDP, TCP, DoH, DoT, etc.)
  - [ ] IP address binding configuration
  - [ ] Port configurations (non-standard ports)
  - [ ] EDNS client subnet settings
  - [ ] QNAME minimization settings
  - [ ] DNS cache size and parameters
  - [ ] Prefer IPv6 settings
- [ ] Recursive resolver settings
  - [ ] Root server configuration
  - [ ] Forwarder configuration
  - [ ] DNSSEC validation settings
- [ ] Export full DNS settings via Web Console
  ```
  Settings > DNS Settings > Export
  ```

## Zone Data
- [ ] List of all authoritative zones
  ```
  Dashboard > Zones > Export All Zones
  ```
- [ ] For each zone:
  - [ ] Zone type (Primary, Secondary, Forwarder)
  - [ ] Export zone file in standard format
  - [ ] DNSSEC signing status and key information
  - [ ] Zone transfer settings (if secondary zone)
  - [ ] Notification settings
  - [ ] Custom record TTLs

## DNS Records
- [ ] For each zone, export all DNS records
  - [ ] A/AAAA records (especially for critical services)
  - [ ] CNAME records
  - [ ] MX records and priorities
  - [ ] TXT records (including SPF, DKIM, DMARC)
  - [ ] SRV records
  - [ ] PTR records (reverse lookup zones)
  - [ ] NS records
  - [ ] CAA records
  - [ ] TLSA records
  - [ ] Any other custom record types
- [ ] Dynamic DNS update configurations
- [ ] Export records via Web Console or API for each zone
  ```
  Zones > [zone name] > Export Zone File
  ```

## DHCP Integration
- [ ] DHCP lease to DNS record registration settings
- [ ] Static DHCP leases with DNS registration
- [ ] DHCP scopes and options that affect DNS
- [ ] DHCP-DNS update security settings

## DNS Forwarding Configuration
- [ ] Default forwarder settings
- [ ] Conditional forwarders
  - [ ] Domain names
  - [ ] Target DNS servers
  - [ ] Forwarder flags/options
- [ ] DNS over HTTPS (DoH) forwarders
- [ ] DNS over TLS (DoT) forwarders
- [ ] Export forwarder configuration

## DNS Blocking and Filtering
- [ ] Blocked domain lists
- [ ] Blocked zone files
- [ ] Allowed domain lists
- [ ] Custom blocking rules
- [ ] Block list URLs for automatic updates
- [ ] Client group filtering rules
- [ ] Block page configuration (if enabled)
- [ ] Export blocking configuration
  ```
  Settings > Blocking > Export
  ```

## Apps and Special Configurations
- [ ] Web Console access settings
- [ ] Recursion control settings
- [ ] Cache control settings
- [ ] Local domain name configuration
- [ ] Split DNS configurations for internal domains
- [ ] Certificate details for secure services
- [ ] Logging settings and log retention policy

## DNS Client Configuration
- [ ] Inventory of devices with static DNS settings
- [ ] Documentation of special client configurations
- [ ] DNS server order in DHCP options
- [ ] DNS suffix search orders

## Authentication and Access Control
- [ ] Admin accounts and credentials (secure export)
- [ ] API access keys
- [ ] IP address restrictions
- [ ] Token-based authentication settings
- [ ] User permissions and role assignments

## Logs and Statistics
- [ ] Current log settings (query logging, error logging)
- [ ] Statistics collection configuration
- [ ] Top queried domains report (for reference)
- [ ] Cache hit ratio and performance metrics
- [ ] Export logs for baseline/historical reference

## Backup and Scheduled Tasks
- [ ] Scheduled backup configuration
- [ ] Automatic update settings
- [ ] Other scheduled maintenance tasks
- [ ] Download complete configuration backup
  ```
  Settings > Advanced > Backup Configuration
  ```

## Custom Features
- [ ] Custom DNS scripts or apps
- [ ] Special response overrides
- [ ] Custom response modification rules
- [ ] API integrations with other systems
- [ ] Custom DNS response policies

## Multi-VLAN Configuration
- [ ] DNS visibility settings per VLAN
- [ ] Access control rules per network segment
- [ ] Different responses based on source network
- [ ] Network interfaces bound to specific VLANs

## Feature Mapping for UDMP Max
- [ ] Create mapping of Technitium features to UDMP Max DNS features
- [ ] Identify potential feature gaps
- [ ] Plan for workarounds where features don't exist in UDMP Max
- [ ] Prioritize critical vs. nice-to-have features

## Pre-Migration Testing
- [ ] Create test queries for all critical DNS record types
- [ ] Generate baseline resolution times for key domains
- [ ] Document propagation times for DNS updates
- [ ] Test client behavior with current DNS settings

## Migration Planning
- [ ] Identify dependency order for DNS services
- [ ] Create staged migration timeline (per your ADR document)
- [ ] Prepare communication for service users
- [ ] Define success criteria for migration
- [ ] Prepare rollback strategy
- [ ] Consider temporary DNS architecture during transition

## Post-Migration Validation
- [ ] Test all authoritative zones
- [ ] Test all conditional forwarders
- [ ] Validate DHCP-to-DNS registration
- [ ] Check response times and cache performance
- [ ] Verify blocking and filtering functionality
- [ ] Test local hostname resolution across VLANs
- [ ] Confirm DNS resolution matches expected behavior for all VLANs

## Documentation
- [ ] Document final Technitium configuration
- [ ] Create mapping document from Technitium to UDMP Max settings
- [ ] Document any custom configurations that need special handling
- [ ] Record important DNS propagation times for reference
- [ ] Update network documentation with new DNS architecture
- [ ] Document workarounds for missing features
- [ ] Create DNS troubleshooting guide for the new environment

---

*This checklist aligns with your existing ADR document "DNS Migration from Technitium to UDMP Max" and should be completed before beginning the migration process.*