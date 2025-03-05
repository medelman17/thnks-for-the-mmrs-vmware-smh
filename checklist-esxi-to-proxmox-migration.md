# ESXi to Proxmox Migration: Configuration Extraction Checklist

## Host System Information
- [ ] ESXi version and build number
- [ ] Hardware specifications (CPU, RAM, storage controllers)
- [ ] Server model and firmware versions
- [ ] Host uptime and performance metrics
- [ ] ESXi license information
- [ ] Advanced system settings and custom parameters
- [ ] Installed VIBs (ESXi packages)
  ```
  esxcli software vib list
  ```
- [ ] PCI device passthrough configurations
  ```
  esxcli hardware pci list
  ```

## Virtual Machine Inventory
- [ ] Complete VM inventory with resource allocations
  ```
  vim-cmd vmsvc/getallvms
  ```
- [ ] VM states (running, stopped, suspended)
- [ ] VM-to-host assignments
- [ ] Resource pools and their configurations
- [ ] VM folders and organizational structure
- [ ] Export VM configuration details
  ```
  vim-cmd vmsvc/get.config [vmid]
  ```

## Storage Configuration
- [ ] Local datastore configuration
- [ ] Storage adapters and their configurations
  ```
  esxcli storage core adapter list
  ```
- [ ] HBA configuration for TrueNAS storage
  ```
  esxcli storage core adapter list
  esxcli storage core device list
  ```
- [ ] VMFS datastore details
  ```
  esxcli storage vmfs extent list
  esxcli storage filesystem list
  ```
- [ ] NFS datastore mount configurations
- [ ] iSCSI target configurations
- [ ] Multipathing policies
  ```
  esxcli storage nmp device list
  ```
- [ ] Storage I/O Control settings
- [ ] VAAI (vStorage API for Array Integration) settings

## Network Configuration
- [ ] Physical NIC inventory and specifications
  ```
  esxcli network nic list
  ```
- [ ] vSwitch configurations
  ```
  esxcli network vswitch standard list
  ```
- [ ] Port group definitions and VLAN tags
  ```
  esxcli network vswitch standard portgroup list
  ```
- [ ] Distributed switch configuration (if used)
- [ ] VMkernel interface configurations
  ```
  esxcli network ip interface list
  esxcli network ip interface ipv4 get
  ```
- [ ] Static routes
  ```
  esxcli network ip route ipv4 list
  ```
- [ ] DNS and hostname configuration
  ```
  esxcli network ip dns server list
  ```
- [ ] NIC teaming policies
- [ ] Network security policies
- [ ] Traffic shaping settings
- [ ] Physical NIC driver versions and settings
  ```
  esxcli network nic get -n vmnicX
  ```

## Virtual Machines Details
For each VM:
- [ ] VM hardware version
- [ ] vCPU configuration (cores, sockets, reservation)
- [ ] Memory allocation (size, reservation, limit)
- [ ] Virtual disk details (size, format, controller type)
  ```
  vmkfstools -D /vmfs/volumes/datastore/vm-name/vm-disk.vmdk
  ```
- [ ] Network adapter type and connection details
- [ ] CD/DVD and removable media configurations
- [ ] Advanced VM parameters
- [ ] VM snapshot inventory
  ```
  vim-cmd vmsvc/snapshot.get [vmid]
  ```
- [ ] VMware Tools status
- [ ] Virtual device PCI slot assignments
- [ ] USB and other device passthrough settings
- [ ] Boot options and EFI/BIOS settings
- [ ] VM guest OS customization
- [ ] Resource allocation settings (shares, limits, reservations)

## ESXi Host Settings
- [ ] Power management settings
- [ ] CPU power policy
- [ ] Memory overcommit settings
- [ ] High availability (HA) configuration
- [ ] Distributed resource scheduler (DRS) settings
- [ ] Host profiles (if used)
- [ ] SNMP configuration
- [ ] NTP settings
  ```
  cat /etc/ntp.conf
  ```
- [ ] Syslog configuration
- [ ] Firewall rules and service configurations
  ```
  esxcli network firewall get
  esxcli network firewall ruleset list
  ```
- [ ] User accounts and permissions
- [ ] SSH configuration
- [ ] Lockdown mode settings
- [ ] Hardware health status

## PCIe Passthrough Config for TrueNAS and OPNsense
- [ ] Detailed documentation of PCI passthrough devices
  ```
  esxcli hardware pci list
  ```
- [ ] VM configurations using passed-through devices
- [ ] Device IDs and physical locations
- [ ] Host boot options for passthrough (IOMMU, etc.)
  ```
  esxcli system settings advanced list | grep -i iommu
  ```
- [ ] Reservation and interrupt settings for passthrough devices
- [ ] Document physical connections with photographs

## Performance Metrics and Baselines
- [ ] CPU utilization (average and peak)
- [ ] Memory utilization (average and peak)
- [ ] Network throughput
- [ ] Storage I/O performance
- [ ] VM performance statistics
- [ ] Resource contention periods
- [ ] Export performance data for baseline comparison

## Backup and Recovery
- [ ] Current backup methods and schedules
- [ ] Backup storage locations and capacities
- [ ] VM snapshot policies
- [ ] Export VM inventories
- [ ] Document recovery procedures
- [ ] Verify all VM backups are current before migration

## Distributed Services (if used)
- [ ] vCenter configuration
- [ ] Cluster settings
- [ ] Distributed resource scheduler (DRS) rules
- [ ] Storage DRS settings
- [ ] High availability (HA) configuration
- [ ] Fault tolerance settings
- [ ] EVC (Enhanced vMotion Compatibility) mode

## License and Support Information
- [ ] Document all ESXi/vSphere licenses
- [ ] Support contract information
- [ ] License keys for any additional software
- [ ] Export license reports

## Migration Planning
- [ ] VM migration priority list
- [ ] Dependency mapping between VMs
- [ ] Maintenance window requirements
- [ ] Applications requiring special handling
- [ ] Create timeline for each host migration
- [ ] Define success criteria for each migrated host
- [ ] Resource allocation plan for Proxmox
- [ ] Network cutover plan

## Specific VM Export Preparation
- [ ] For TrueNAS VMs:
  - [ ] Document exact storage controller settings
  - [ ] Verify zpool status before export
  - [ ] Create ZFS snapshots as migration points
- [ ] For OPNsense VMs:
  - [ ] Export complete configuration backup
  - [ ] Document interface mappings
  - [ ] Note any special hardware requirements

## Proxmox Pre-Configuration Planning
- [ ] Storage configuration mapping (ESXi to Proxmox)
- [ ] Network configuration mapping (vSwitch to Linux bridges)
- [ ] VM conversion format selection (raw, qcow2, etc.)
- [ ] Passthrough device mapping
- [ ] User permission structure
- [ ] Cluster configuration (if applicable)
- [ ] Storage migration path

## Documentation
- [ ] Create visual network topology diagram
- [ ] Document physical to virtual machine mappings
- [ ] Create detailed hardware inventory
- [ ] Document custom scripts or automation
- [ ] Update IP address and hostname documentation
- [ ] Create service dependency map
- [ ] Document current performance baselines

## Pre-Migration Verification
- [ ] Test VM exports and conversions with non-critical VMs
- [ ] Verify hardware compatibility with Proxmox
- [ ] Test PCI passthrough in Proxmox environment
- [ ] Verify storage performance in test environment
- [ ] Check for any ESXi-specific guest customizations
- [ ] Verify network path redundancy designs will work in Proxmox

## Rollback Planning
- [ ] Define trigger conditions for rollback
- [ ] Document step-by-step rollback procedure
- [ ] Verify backup integrity for rollback scenarios
- [ ] Test restore procedures when possible
- [ ] Document point-of-no-return stages in migration

---

*This checklist should be completed for each ESXi host before beginning the migration to Proxmox. Pay special attention to PCI passthrough configurations for HBAs and network cards that are critical for TrueNAS and OPNsense VMs.*