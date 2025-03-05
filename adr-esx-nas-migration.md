# Architectural Decision Record: Storage Migration from Virtualized TrueNAS to Proxmox-based Solution

## Status
Proposed

## Context
The current home network infrastructure uses virtualized TrueNAS running on ESXi hosts with PCIe HBA cards passed through to provide access to physical storage arrays. As part of a larger migration from virtualized networking to hardware-based solutions, the storage infrastructure needs to be migrated while preserving data integrity and minimizing downtime. This migration is constrained by licensing limitations that prevent spinning up the primary TrueNAS VM on ESXi and limited ability to allocate new hardware resources.

## Current State
- Two virtualized TrueNAS instances running on separate ESXi hosts
- Primary NAS: 16x8TB HDDs (128TB raw) on 12Gb SAS via HBA passthrough
- Secondary NAS: 24x1TB SSDs (24TB raw) via HBA passthrough
- TrueNAS VM export is available but cannot be redeployed due to licensing
- ESXi hosts will be repurposed as part of the infrastructure migration

## Decision
**Perform in-place conversion of ESXi hosts to Proxmox with virtualized TrueNAS** as part of the broader network consolidation strategy.

### Justification
1. **Minimized Hardware Manipulation**: Converting in-place reduces risk to storage arrays by avoiding physical disk movement
2. **Alignment with Infrastructure Goals**: Consistent with broader migration to Proxmox
3. **Resource Efficiency**: Repurposes existing hardware without requiring additional resources
4. **Flexibility**: Maintains virtualization benefits while preserving storage arrangements
5. **Reduced Risk**: ZFS pool import provides data integrity without requiring original VM to boot

## Consequences

### Positive
- Preservation of existing storage configurations and connections
- Reduced risk compared to physical hardware movement
- Integration with planned Proxmox infrastructure
- Potential for future storage flexibility with virtualized NAS
- Pathway to CEPH implementation in the future if desired

### Negative
- Temporary complete storage outage during conversion
- Potential for configuration complexities during VM recreation
- Risk associated with ZFS pool imports without original VM
- Manual recreation of sharing and service configurations required

### Neutral
- Similar management paradigm (virtual machine-based NAS)
- No immediate gain in raw performance over previous solution
- Maintenance requirements remain similar to current approach

## Alternatives Considered

### Bare Metal TrueNAS Deployment
- **Pro**: Potentially improved performance with direct hardware access
- **Pro**: Simplified storage I/O path
- **Con**: Less flexible for future evolution (would require another migration for CEPH)
- **Con**: Same level of hardware manipulation risk as virtualized approach

### Temporary Hardware for Transition
- **Pro**: Reduced downtime during migration
- **Pro**: Lower risk than direct in-place conversion
- **Con**: Requires additional hardware resources (not available)
- **Con**: More complex migration path with additional steps

### Hybrid Bare Metal + Virtualized Approach
- **Pro**: Could optimize for specific workloads
- **Pro**: Distributes risk across multiple systems
- **Con**: More complex to manage long-term
- **Con**: Requires more extensive reconfiguration

## Implementation Plan

### Phase 1: Preparation (1-2 Days)
1. Document current ESXi and TrueNAS configurations:
   - Hardware inventory and connection details
   - Network configurations and IP addressing
   - Pool configurations and dataset structures
   - Share configurations (SMB, NFS, iSCSI)
   - User permissions and access controls
   - Service configurations
2. Create configuration backups of both TrueNAS instances
3. Document ESXi PCIe passthrough configurations
4. Create ZFS snapshots on all pools: `zfs snapshot -r poolname@pre_migration`
5. Prepare Proxmox installation media
6. Verify TrueNAS VM exports are complete and accessible

### Phase 2: In-Place Conversion (Maintenance Window)
1. Shut down all VMs and put ESXi host in maintenance mode
2. Document physical connections with photographs
3. Install Proxmox VE on the ESXi host system (targeting the same boot device)
4. Configure networking to match previous settings
5. Install any required HBA drivers and verify hardware detection
6. Configure PCIe passthrough for HBA cards

### Phase 3: TrueNAS VM Deployment (Same Maintenance Window)
1. Create new VM in Proxmox matching original specifications
2. Configure PCIe passthrough of HBA card to VM
3. Options for TrueNAS deployment:
   a. Fresh TrueNAS installation if VM import not feasible
   b. Import TrueNAS VM disk from export if possible
4. Boot TrueNAS VM and verify HBA detection
5. Import existing ZFS pools using appropriate commands:
   ```
   zpool import
   zpool import poolname
   ```
6. Verify pool status and dataset integrity

### Phase 4: Service Reconstruction (1-2 Days)
1. Configure networking in TrueNAS to match previous settings
2. Recreate sharing configurations (SMB, NFS, iSCSI)
3. Set up user accounts and permissions
4. Configure any additional services (replication, snapshots, etc.)
5. Update DNS records to point to new TrueNAS instances
6. Test client connectivity and performance

### Phase 5: Secondary NAS Migration (After Primary Stabilization)
1. Repeat the process for secondary NAS
2. Ensure both systems are operational before final decommissioning
3. Reestablish any replication between primary and secondary NAS

### Phase 6: Verification and Optimization (1-2 Days)
1. Perform data integrity verification with `zpool scrub`
2. Test all client access methods
3. Verify performance meets expectations
4. Document final configuration for future reference
5. Implement monitoring and alerting

## Rollback Plan

### Trigger Conditions
- Critical failures during Proxmox installation
- Inability to pass through HBA devices to VMs
- ZFS pool import failures
- Data integrity issues discovered post-migration

### Rollback Steps
1. If ESXi installation is still intact on original boot device:
   - Revert to ESXi installation
   - Attempt to recover original configuration
   - Import ZFS pools into alternative system if necessary

2. If ESXi is overwritten but rollback needed:
   - Implement temporary access to storage via alternative means
   - Plan more extensive recovery operation

## Success Criteria
- All ZFS pools successfully imported with no data loss
- All sharing services operational and accessible to clients
- Performance meets or exceeds previous metrics
- All user permissions correctly applied
- Automated tasks (snapshots, replication) functioning correctly

## Future Considerations
- Evaluate CEPH implementation as a future storage evolution
- Consider backup strategy enhancements with new infrastructure
- Explore performance optimization opportunities in Proxmox
- Plan for long-term capacity expansion

---

*This document should be reviewed and updated as the migration progresses.*

Date: March 5, 2025
