# Architectural Decision Record: DNS Migration from Technitium to UDMP Max

## Status
Approved

## Context
The current home network infrastructure uses virtualized Technitium DNS running on ESXi hosts. As part of a larger migration from virtualized networking to hardware-based solutions centered around the Ubiquiti Dream Machine Pro Max (UDMP Max), the DNS services need to be migrated.

## Current State
- Technitium DNS server running as a virtual machine on ESXi
- Multi-VLAN environment with complex routing between virtualized OPNsense routers
- Dual-WAN connectivity with BGP providing failover between connections
- DNS serving multiple network segments with various security requirements

## Decision
**Migrate DNS services from virtualized Technitium to the UDMP Max** as part of the broader network consolidation strategy.

### Justification
1. **Architectural Simplification**: Consolidating network services reduces operational complexity
2. **Unified Management**: DNS and firewall policies managed from the same interface
3. **Reduced Resources**: Elimination of a dedicated VM reduces virtualization overhead
4. **Integrated Security**: DNS filtering tied directly to firewall and IDS/IPS systems
5. **Consistent Configuration**: Simplified DHCP-DNS integration

## Consequences

### Positive
- Reduced management overhead (one less system to maintain)
- Simplified troubleshooting with integrated logs
- Direct integration with UDMP Max security features
- Streamlined IP reservation and DHCP management
- Consolidated configuration backups

### Negative
- Potential loss of specialized DNS features available in Technitium
- Single point of failure if UDMP Max experiences issues
- May require more frequent reboots for UDMP Max firmware updates
- Possibly less granular control over DNS caching and query logging

### Neutral
- Different management interface for DNS administration
- Adaptation period for network administrators

## Alternatives Considered

### Keep Technitium as Primary, UDMP Max as Secondary
- **Pro**: Maintains advanced features of Technitium
- **Pro**: Provides DNS redundancy
- **Con**: Continues virtualization overhead
- **Con**: Splits management between two systems

### Run Both Systems in Parallel for Different VLANs
- **Pro**: Could dedicate more secure DNS to sensitive VLANs
- **Con**: Increased complexity in troubleshooting
- **Con**: Confusing split management

### Migrate to a Different Dedicated DNS Solution
- **Pro**: Could choose a more feature-rich replacement
- **Con**: Doesn't align with consolidation strategy
- **Con**: Would require additional hardware

## Migration Plan

### Phase 1: Preparation and Assessment (Days 1-2)
1. Document all current Technitium DNS configurations:
   - Custom DNS records
   - Forwarding rules
   - Access control lists
   - Specialized configurations
2. Inventory all devices with static DNS settings
3. Create backup of Technitium configuration
4. Document current DNS performance metrics as baseline

### Phase 2: UDMP Max DNS Configuration (Day 3)
1. Configure UDMP Max DNS settings:
   - Set up appropriate upstream DNS providers
   - Configure DNS caching parameters
   - Import custom DNS records from Technitium
   - Set up conditional forwarding if needed
   - Configure DNS filtering rules
2. Test DNS resolution locally from UDMP Max
3. Validate DHCP-to-DNS registration

### Phase 3: Limited Deployment (Days 4-6)
1. Configure test devices to use UDMP Max as primary DNS
2. Test resolution across all planned VLANs
3. Monitor and troubleshoot any resolution issues
4. Verify DNS filtering and security policies
5. Test local name resolution
6. Document performance metrics

### Phase 4: Gradual Cutover (Days 7-10)
1. Update DHCP to issue UDMP Max as primary DNS server
2. Migrate one VLAN at a time, in order of increasing criticality:
   - Guest network (VLAN 40)
   - IoT network (VLAN 30)
   - General network (VLAN 1)
   - Server network (VLAN 20)
   - Management network (VLAN 10)
3. Keep Technitium as secondary DNS during transition
4. Monitor DNS query performance and error rates
5. Address any issues before proceeding to next VLAN

### Phase 5: Finalization (Days 11-14)
1. Remove Technitium as secondary DNS from DHCP configuration
2. Update any devices with static DNS configurations
3. Monitor system for 3-4 days at full deployment
4. Document final performance metrics
5. Document lessons learned
6. Update network documentation to reflect new DNS architecture
7. Keep Technitium VM in powered-off state for 2 weeks as backup

### Phase 6: Decommissioning (Day 30)
1. Final verification that all systems function properly with UDMP Max DNS
2. Export final configuration backup from Technitium for archival
3. Decommission Technitium VM
4. Reclaim resources on ESXi host

## Rollback Plan

### Trigger Conditions
- Critical DNS resolution failures affecting multiple devices
- Performance degradation beyond acceptable thresholds
- Security vulnerabilities discovered in UDMP Max DNS implementation

### Rollback Steps
1. Reactivate Technitium VM if decommissioned
2. Revert DHCP settings to use Technitium as primary DNS
3. Update any manually configured devices
4. Verify restoration of DNS services
5. Document issues that triggered rollback for future resolution

## Success Criteria
- All devices can resolve internal and external hostnames
- DNS resolution performance meets or exceeds previous metrics
- DNS filtering rules effectively block malicious domains
- Local hostname resolution works across all VLANs according to security policy
- Management overhead reduced compared to previous solution

## Future Considerations
- Evaluate UDMP Max DNS performance under peak load conditions
- Consider secondary DNS server for redundancy if needed
- Review DNS filtering rules quarterly
- Monitor upstream DNS provider performance
- Explore advanced DNS security features as they become available

---

*This document should be reviewed and updated as the migration progresses.*

Date: March 5, 2025
