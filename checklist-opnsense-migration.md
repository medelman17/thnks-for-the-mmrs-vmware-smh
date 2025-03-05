# OPNsense to UDMP Max Migration: Configuration Extraction Checklist

## System Information
- [ ] OPNsense version and build information
- [ ] Hardware specifications (CPU, RAM, network interfaces)
- [ ] Active services inventory and their status
- [ ] System tunables and custom sysctl settings
- [ ] Current resource utilization baselines (CPU, memory, network throughput)

## Network Interface Configuration
- [ ] Physical interface configurations (names, assignments, drivers)
- [ ] VLAN interfaces and tagging configuration
- [ ] Bridge interfaces and their members
- [ ] Interface IP configurations (IPv4/IPv6 addresses, DHCP configurations)
- [ ] Interface groups
- [ ] Hardware offloading settings
- [ ] MTU configurations
- [ ] Interface statistics and packet loss rates

## WAN Connectivity
- [ ] WAN interface assignments (VLAN 3991/3992)
- [ ] Connection types (DHCP, static, PPPoE)
- [ ] WAN IP addresses, gateways, and DNS servers
- [ ] Multi-WAN configuration details
  - [ ] Gateway groups
  - [ ] Load balancing settings
  - [ ] Failover configurations
- [ ] DNS resolver upstream servers
- [ ] Traffic shaping and QoS settings for WAN interfaces
- [ ] Upstream gateway monitoring configurations

## BGP Configuration
- [ ] BGP global settings (AS number, router ID)
- [ ] Neighbor configurations (VLAN 99 - 192.168.99.0/24)
  - [ ] Neighbor IP addresses
  - [ ] Remote AS numbers
  - [ ] Authentication settings
  - [ ] Timers and keepalive settings
- [ ] Network advertisements
- [ ] Route maps and prefix lists
- [ ] BGP communities configuration
- [ ] Export current BGP routing table
  ```
  ssh root@opnsense "bgpctl show rib"
  ```
- [ ] BGP neighbor status and statistics
  ```
  ssh root@opnsense "bgpctl show neighbor"
  ```

## VLAN Configuration
- [ ] VLAN IDs and parent interfaces (VLANs 1, 99, 1016, 1032, 3991, 3992)
- [ ] VLAN priorities and QoS settings
- [ ] VLAN description and purpose documentation
- [ ] Interface assignments for each VLAN
- [ ] IP addressing scheme for each VLAN
- [ ] PCP/CoS tagging settings

## Routing Configuration
- [ ] Static routes
- [ ] Policy-based routing rules
- [ ] Default gateway configurations
- [ ] Routing protocols configurations (OSPF, RIP if used)
- [ ] Export of current routing table
  ```
  ssh root@opnsense "netstat -rn"
  ```
- [ ] Virtual IPs and CARP configurations
- [ ] Gateway monitoring settings

## Firewall Rules and NAT
- [ ] Floating rules (export with descriptions)
- [ ] Interface-specific rules (for each interface/VLAN)
- [ ] 1:1 NAT mappings
- [ ] Port forwards
- [ ] Outbound NAT configurations
- [ ] NPT/NAT66 rules for IPv6
- [ ] Aliases (IP, ports, URLs)
- [ ] Schedule-based rules
- [ ] Traffic shaping rules
- [ ] Limiter configurations
- [ ] Rule statistics (hit counts) to identify active rules
  ```
  ssh root@opnsense "pfctl -sr -v"
  ```

## VPN Configurations
- [ ] OpenVPN server/client configurations
- [ ] IPsec tunnel settings
- [ ] WireGuard configurations
- [ ] L2TP/PPTP settings (if used)
- [ ] VPN user accounts and certificates
- [ ] VPN client-specific overrides
- [ ] Split tunneling configurations

## DNS Configuration
- [ ] DNS resolver settings
- [ ] DNS forwarder configurations
- [ ] Domain overrides
- [ ] Custom DNS records
- [ ] DNSSEC settings
- [ ] DNS over TLS configurations
- [ ] DNS query forwarding settings
- [ ] Block lists and regex customizations

## DHCP Services
- [ ] DHCP server settings for each interface
- [ ] IP ranges and exclusions
- [ ] DHCP static mappings
- [ ] DHCP options
- [ ] TFTP server settings (if used)
- [ ] Network boot configurations
- [ ] DHCPv6 settings

## Captive Portal Configuration (if used)
- [ ] Zone configurations
- [ ] Authentication methods
- [ ] Allowed IP addresses/ranges
- [ ] Landing page customizations
- [ ] Voucher settings

## Certificates and PKI
- [ ] SSL certificates (export with private keys)
- [ ] Certificate authorities
- [ ] Certificate revocation lists
- [ ] DH parameters

## Authentication and Access Control
- [ ] Local user accounts
- [ ] User groups and privileges
- [ ] External authentication servers (LDAP, RADIUS)
- [ ] Two-factor authentication settings
- [ ] API keys

## IDS/IPS Configuration (Suricata)
- [ ] Enabled rule categories
- [ ] Custom rules
- [ ] Alert settings
- [ ] Performance tuning parameters
- [ ] Blocked hosts and networks
- [ ] Pattern matching settings
- [ ] Suppressed rules

## Proxy and Content Filtering
- [ ] Web proxy configurations
- [ ] Authentication settings
- [ ] ACLs and filtering rules
- [ ] ICAP settings
- [ ] Category-based filtering configurations

## QoS and Traffic Shaping
- [ ] Traffic shaper queues
- [ ] Shaper rules
- [ ] Limiters
- [ ] Prioritization settings
- [ ] DSCP/CoS marking rules

## High Availability Configuration
- [ ] CARP virtual IPs
- [ ] Synchronization settings
- [ ] Failover trigger configurations
- [ ] State synchronization settings

## System Backup
- [ ] Complete OPNsense XML configuration backup
  ```
  System→Configuration→Backups→Download configuration
  ```
- [ ] RRD data export (for historical graphs)
- [ ] Custom scripts and templates
- [ ] Plugin-specific configuration exports

## Logging and Monitoring
- [ ] Syslog server configurations
- [ ] Log forwarding settings
- [ ] Netflow/sFlow configurations
- [ ] SNMP settings
- [ ] Alert configurations
- [ ] Monitoring dashboard layouts

## Migration Testing Plan
- [ ] Identify critical services for testing
- [ ] Define success criteria for each service
- [ ] Create test procedures for routing functionality
- [ ] Develop verification steps for security policies
- [ ] Plan for parallel operation during transition
- [ ] Create rollback procedure

## UDMP Max Feature Gap Analysis
- [ ] Identify OPNsense features currently in use
- [ ] Map features to UDMP Max equivalents
- [ ] Document features without direct equivalents
- [ ] Plan workarounds for missing functionality
- [ ] Prioritize critical vs. nice-to-have features

## Documentation
- [ ] Network diagrams with current router placement
- [ ] Current traffic flows for critical services
- [ ] Connection diagrams for physical interfaces
- [ ] Performance baselines for comparison
- [ ] Troubleshooting steps for common issues
- [ ] Contact information for service providers

## Pre-Migration Actions
- [ ] Take screenshots of critical configuration sections
- [ ] Export interface statistics for baseline comparisons
- [ ] Document current throughput performance
- [ ] Run packet captures for critical protocols to understand normal traffic patterns
- [ ] Verify configuration backup integrity
- [ ] Test restore procedure on a virtual environment if possible

---

*This checklist should be completed for both virtualized OPNsense routers before beginning migration to the UDMP Max.*