# Architectural Decision Record: Sequential NAS Migration Strategy Using Active Secondary NAS

## Status
Proposed

## Context
As part of the broader infrastructure migration from ESXi to Proxmox, two virtualized TrueNAS systems need to be migrated. The primary NAS (16x8TB HDDs) and secondary NAS (24x1TB SSDs) are both currently running as VMs on separate ESXi hosts. Since the secondary NAS will remain operational during the initial migration phases, there's an opportunity to leverage it to enhance the migration process and reduce overall risk.

## Current State
- Primary NAS: 16x8TB HDDs (128TB raw) on 12Gb SAS via HBA passthrough
- Secondary NAS: 24x1TB SSDs (24TB raw) via HBA passthrough
- Both systems running as VMs on separate ESXi hosts
- Secondary NAS will remain operational while primary NAS is migrated
- Limited hardware resources prevent allocating new systems for the migration

## Decision
**Implement a sequential migration strategy that leverages the still-operational secondary NAS as a reference system and temporary data repository during the primary NAS migration.**

### Justification
1. **Risk Reduction**: Ensures at least one NAS system remains operational throughout the migration process
2. **Data Protection**: Provides an additional backup location for critical data during migration
3. **Configuration Reference**: Maintains access to a live reference system for configurations and settings
4. **Resource Efficiency**: Maximizes use of existing infrastructure without requiring additional hardware
5. **Service Continuity**: Enables temporary service relocation options for critical functions

## Consequences

### Positive
- Always maintains one operational NAS system throughout the migration
- Provides an additional layer of data protection for critical datasets
- Creates a live reference system for configuration validation
- Enables better testing and verification before complete cutover
- Supports phased service migration rather than an all-at-once approach

### Negative
- Extends the overall migration timeline compared to simultaneous conversion
- Temporarily increases network load on secondary NAS during primary migration
- Requires additional planning for temporary service relocations
- May require user retraining for temporarily relocated services

### Neutral
- Overall migration complexity remains similar
- Total downtime across all services remains approximately the same

## Alternatives Considered

### Simultaneous Migration of Both NAS Systems
- **Pro**: Shorter overall migration timeline
- **Pro**: Simplified project management with single migration event
- **Con**: Higher risk with all storage systems offline simultaneously
- **Con**: No fallback system during the migration process
- **Con**: No reference system available for troubleshooting

### Migrate Secondary NAS First
- **Pro**: Lower initial impact if issues arise (smaller dataset)
- **Pro**: Establishes migration process on less critical system
- **Con**: Doesn't leverage SSD performance advantages during migration
- **Con**: Limits options for temporary critical data protection

### Temporary Third-Party Storage Solution
- **Pro**: Independent backup system during migration
- **Pro**: Reduced interdependencies between systems
- **Con**: Additional costs and resource requirements
- **Con**: Increased complexity with another system to manage

## Implementation Plan

### Phase 1: Preparation and Replication Setup (Days 1-3)
1. Document configurations for both NAS systems:
   - Network settings and IP addressing
   - Pool configurations and dataset structures
   - Share configurations (SMB, NFS, iSCSI)
   - User permissions and access controls
   - Service configurations and schedules
2. Identify critical datasets on primary NAS that should be temporarily replicated
3. Create dedicated datasets on secondary NAS for temporary migration data
4. Configure and test replication from primary to secondary NAS
   ```
   # Create target dataset on secondary NAS
   zfs create secondarypool/migration_backup
   
   # On primary NAS, create pre-migration snapshot
   zfs snapshot -r primarypool/critical_data@pre_migration
   
   # Configure replication task in TrueNAS UI or via CLI
   ```
5. Update DNS and service documentation to prepare for temporary relocations
6. Verify secondary NAS has sufficient performance headroom for additional workload
7. Test failover procedures for any services that might temporarily migrate

### Phase 2: Primary NAS Pre-Migration Service Adjustments (Day 4)
1. Implement temporary service relocations as needed:
   - Update DNS records for services being temporarily relocated
   - Redirect clients to secondary NAS for specific functions
   - Communicate changes to affected users
2. Execute final replication of critical data to secondary NAS
3. Create comprehensive ZFS snapshots on primary NAS
   ```
   zfs snapshot -r primarypool@final_pre_migration
   ```
4. Document exact configuration state of primary NAS for reference

### Phase 3: Primary NAS Migration (Days 5-7)
1. Shut down primary TrueNAS VM
2. Perform in-place conversion of ESXi host to Proxmox
   - Follow detailed steps from previous ADR for in-place conversion
   - Install Proxmox VE on the ESXi host system
   - Configure networking to match previous settings
   - Set up PCIe passthrough for HBA card
3. Create and configure new TrueNAS VM
4. Import existing ZFS pools from HBA-connected storage
5. Restore service configurations using documentation and secondary NAS as reference
6. Verify all datasets and data integrity

### Phase 4: Primary NAS Service Restoration (Days 8-9)
1. Configure sharing services (SMB, NFS, iSCSI)
2. Set up user accounts and permissions
3. Test service functionality and performance
4. Gradually restore services to primary NAS from secondary NAS
5. Update DNS records to point back to primary NAS for restored services
6. Validate client access and functionality

### Phase 5: Secondary NAS Migration (Days 10-12)
1. Configure replication of secondary-only data to now-operational primary NAS
   ```
   # On secondary NAS
   zfs snapshot -r secondarypool/unique_data@pre_migration
   
   # Configure replication to primary NAS
   ```
2. Execute final data synchronization
3. Repeat the ESXi to Proxmox conversion process for secondary NAS host
   - Shut down secondary TrueNAS VM
   - Install Proxmox VE
   - Configure PCIe passthrough
   - Deploy TrueNAS VM
   - Import SSD pools
4. Restore sharing and service configurations
5. Update DNS records to reflect final configuration

### Phase 6: Post-Migration Configuration (Days 13-14)
1. Reestablish normal replication between primary and secondary NAS
2. Fine-tune performance settings on both systems
3. Verify all automation and scheduled tasks
4. Update documentation to reflect new environment
5. Run performance and integrity verification on both systems

## Rollback Plan

### Primary NAS Migration Issues
1. Continue operating with secondary NAS providing critical services
2. Consider direct access to primary storage if TrueNAS import fails:
   - Connect HBA to another system temporarily
   - Import ZFS pools in read-only mode for data recovery
3. Extend secondary NAS service hosting while troubleshooting

### Secondary NAS Migration Issues
1. Since primary NAS is already operational:
   - Prioritize data recovery from secondary storage
   - Import pools to primary NAS temporarily if needed
2. Consider running secondary TrueNAS as standard VM without HBA passthrough temporarily

## Success Criteria
- All data preserved and accessible on both NAS systems
- All original services restored to appropriate NAS systems
- Performance meets or exceeds pre-migration levels
- Bidirectional replication functioning correctly
- All client access methods operational
- All scheduled tasks and automation functioning

## Future Considerations
- Evaluate performance of virtualized NAS vs. bare metal
- Consider CEPH integration possibilities with Proxmox
- Review backup strategies with new infrastructure
- Develop disaster recovery plan for new environment
- Document lessons learned for future storage migrations

---

*This document should be reviewed and updated as the migration progresses.*

Date: March 5, 2025
