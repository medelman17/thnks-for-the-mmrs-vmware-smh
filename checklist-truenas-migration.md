# TrueNAS Pre-Migration Configuration Extraction Checklist

## System Information
- [ ] TrueNAS version and build number
- [ ] Base OS information (FreeBSD version or Linux distribution for SCALE)
- [ ] Hardware configuration inventory (CPU, RAM, network cards)
- [ ] HBA card model, firmware version, and driver information
- [ ] PCIe slot positioning of the HBA card
- [ ] System tunables and sysctl settings

## Network Configuration
- [ ] Interface configurations and IP addresses
- [ ] VLAN tagging settings
- [ ] Static routes
- [ ] Default gateway
- [ ] DNS server settings
- [ ] Hostname and domain settings
- [ ] NTP configuration
- [ ] Network service configurations (SSH, etc.)

## Storage Configuration
- [ ] ZFS pool names and configurations
  ```
  zpool status -v
  zpool list -v
  ```
- [ ] ZFS dataset structure and properties
  ```
  zfs list -r -o name,mountpoint,compression,recordsize,atime,exec,quota,reservation
  ```
- [ ] ZFS snapshot inventory
  ```
  zfs list -t snapshot
  ```
- [ ] SMART test schedules
- [ ] Scrub schedules
- [ ] Disk serial numbers and physical locations
  ```
  camcontrol devlist (FreeBSD/Core)
  lsblk -o NAME,SERIAL,SIZE (Linux/SCALE)
  ```
- [ ] Virtual device (vdev) configuration details
- [ ] Any special pool features or flags
  ```
  zpool get all
  ```

## Sharing Configurations
- [ ] SMB shares and their properties
  - Share names and paths
  - Permissions
  - Access control lists
  - Special parameters
- [ ] NFS exports and their properties
  - Export paths
  - Client restrictions
  - Access options
- [ ] iSCSI target configurations
  - Target names and IQNs
  - LUN mappings
  - CHAP authentication settings
  - Initiator restrictions
- [ ] S3 object storage configuration (if used)
- [ ] WebDAV settings (if used)
- [ ] FTP/SFTP configurations (if used)

## User and Permission Management
- [ ] Local user accounts and IDs
  ```
  cat /etc/passwd
  ```
- [ ] Local group configurations
  ```
  cat /etc/group
  ```
- [ ] Directory service configurations (AD, LDAP, etc.)
  - Server addresses
  - Bind credentials (securely)
  - Search base and schema settings
- [ ] User and group quotas
- [ ] Privileges and permission schemes
- [ ] ACL configurations for critical datasets

## Scheduled Tasks
- [ ] Snapshot schedules
- [ ] Replication tasks
  - Source and target datasets
  - Transport options
  - Frequency and retention
- [ ] Scrub schedules
- [ ] SMART test schedules
- [ ] Rsync tasks
- [ ] Custom cron jobs or scripts
  ```
  crontab -l
  ```

## Replication and Backup
- [ ] Replication configuration between primary and secondary NAS
- [ ] Remote replication configurations (if any)
- [ ] Cloud backup settings (if configured)
- [ ] Retention policies for backups and replications
- [ ] Verification methods for backups

## Service Configurations
- [ ] Service enablement status
  ```
  service -e (FreeBSD/Core)
  systemctl list-unit-files --state=enabled (Linux/SCALE)
  ```
- [ ] Service startup settings
- [ ] Service tuning parameters
- [ ] Plugin or jail configurations (TrueNAS Core)
- [ ] Container/app configurations (TrueNAS SCALE)

## Advanced Configurations
- [ ] Alert settings and notifications
- [ ] Custom API keys
- [ ] Certificate configurations
- [ ] SNMP settings
- [ ] Syslog configurations
- [ ] Reporting and metrics collection settings
- [ ] Any custom shell scripts or tools

## Identifying Critical Data
- [ ] Datasets containing irreplaceable data
- [ ] Service priorities for restoration
- [ ] Application data dependencies
- [ ] Maximum acceptable downtime for each service

## Pre-Migration Health Checks
- [ ] Perform ZFS scrub and verify completion
  ```
  zpool scrub poolname
  zpool status
  ```
- [ ] Run SMART tests on all drives
- [ ] Check system logs for errors
  ```
  cat /var/log/messages
  dmesg | tail
  ```
- [ ] Verify all replication tasks are successful
- [ ] Test restore from recent snapshots

## Configuration Extraction Methods
- [ ] Create TrueNAS configuration backup file via UI
- [ ] Extract configuration database
  ```
  # TrueNAS Core
  cp /data/freenas-v1.db /tmp/
  
  # TrueNAS SCALE
  cp /data/middlewared.db /tmp/
  ```
- [ ] Archive critical configuration directories
  ```
  tar -czf /tmp/etc_backup.tar.gz /etc
  ```
- [ ] Take screenshots of critical UI configurations
- [ ] Generate pool/dataset/sharing reports

## Documentation
- [ ] Create network diagram with IP addresses and connections
- [ ] Document physical disk-to-vdev mapping
- [ ] Create service dependency map
- [ ] Document external integration points
- [ ] List client systems and their access methods
- [ ] Record specific performance metrics for baseline comparison

## Pre-Migration Actions
- [ ] Create comprehensive ZFS snapshots
  ```
  zfs snapshot -r poolname@pre_migration
  ```
- [ ] Export configuration backup via UI
- [ ] Test service failover to secondary NAS
- [ ] Document exact HBA cabling with photographs
- [ ] Prepare users for potential service disruptions

---

*This checklist should be completed for both NAS systems before beginning migration.*
