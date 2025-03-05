# DNS Naming Scheme and Cloudflare Configuration

## Internal Naming Structure for `edel.host`

### VLAN-Based Subdomains

| VLAN ID | Network CIDR    | Subdomain Pattern              | Example Usage                    |
|---------|-----------------|--------------------------------|----------------------------------|
| 1       | 192.168.1.0/24  | `*.home.edel.host`             | General home devices and clients |
| 10      | 10.10.0.0/24    | `*.mgmt.edel.host`             | Network management devices       |
| 20      | 10.20.0.0/24    | `*.srv.edel.host`              | Server infrastructure            |
| 30      | 10.30.0.0/24    | `*.iot.edel.host`              | IoT and smart home devices       |
| 40      | 10.40.0.0/24    | `*.guest.edel.host`            | Guest network services           |

### Naming Conventions by Device Type

#### Network Infrastructure
- Gateway: `gateway.mgmt.edel.host` (UDMP Max)
- Core Switch: `core-switch.mgmt.edel.host` (Mikrotik CRS504-4XQ)
- Distribution Switches: 
  - `dist-switch-1.mgmt.edel.host` (Mikrotik CRS312)
  - `dist-switch-2.mgmt.edel.host` (QNAP Switch)
  - `dist-switch-3.mgmt.edel.host` (Ubiquiti Switch)
- Wireless APs: `ap-[location].mgmt.edel.host` (e.g., `ap-living.mgmt.edel.host`)

#### Servers & Services
- `[service-name].srv.edel.host` (e.g., `plex.srv.edel.host`, `nas.srv.edel.host`)
- `[function]-[instance].srv.edel.host` (e.g., `db-primary.srv.edel.host`, `web-01.srv.edel.host`)

#### Client Devices
- Desktop/Laptop: `pc-[username/location].home.edel.host` (e.g., `pc-office.home.edel.host`)
- Mobile: `mob-[username].home.edel.host` (e.g., `mob-john.home.edel.host`)
- Tablets: `tab-[username].home.edel.host` (e.g., `tab-jane.home.edel.host`)
- Printers: `prn-[location].home.edel.host` (e.g., `prn-office.home.edel.host`)

#### IoT Devices
- `[device-type]-[location].iot.edel.host` (e.g., `cam-front.iot.edel.host`, `thermo-living.iot.edel.host`)

#### Service Aliases (Function-Based)
Create service aliases that point to actual server hostnames:
- `files.edel.host` → Points to your file server
- `media.edel.host` → Points to your media server
- `backup.edel.host` → Points to your backup system
- `git.edel.host` → Points to your source control system

## Cloudflare Configuration for `edel.host`

### Split DNS Architecture

Implement a split-horizon DNS setup:
- **Internal DNS Provider**: UDMP Max
- **External DNS Provider**: Cloudflare
- **Domain**: `edel.host`

This design ensures:
- Internal devices resolve `*.edel.host` to private IP addresses
- External requests resolve only to public-facing services
- No DNS leakage of internal network architecture

### Cloudflare DNS Configuration

#### 1. Public-Facing Records

| Type  | Name             | Value             | TTL    | Proxy Status     |
|-------|------------------|-------------------|--------|------------------|
| A     | vpn.edel.host    | [Public IP]       | 1 hour | DNS Only (Gray)  |
| A     | remote.edel.host | [Public IP]       | 1 hour | Proxied (Orange) |
| CNAME | www.edel.host    | edel.host         | Auto   | Proxied (Orange) |

Only create Cloudflare DNS records for services that need to be publicly accessible.

#### 2. Security Settings

Enable these Cloudflare security features:
- **SSL/TLS**: Full (Strict) mode
- **DNSSEC**: Enabled and validated
- **HTTPS Redirects**: Enabled
- **Always Use HTTPS**: Enabled
- **Email Security**: Configure SPF, DKIM, and DMARC records if using email

#### 3. CAA Records

Add Certificate Authority Authorization records:
```
0 issue "letsencrypt.org"
0 issuewild ";"
0 iodef "mailto:admin@edel.host"
```

#### 4. Firewall Rules

Create Cloudflare firewall rules for public-facing services:
- Block access to admin interfaces except from trusted IPs
- Implement rate limiting for login attempts
- Set up geographic restrictions if appropriate

### UDMP Max DNS Configuration

#### 1. Local Zone Setup

Configure UDMP Max to:
- Act as authoritative for `edel.host` locally
- Create local DNS records for all internal hosts
- Implement conditional forwarding for non-local domains

#### 2. Local Records Example

| Hostname                  | IP Address      | Type |
|---------------------------|-----------------|------|
| gateway.mgmt.edel.host    | 10.10.0.1       | A    |
| core-switch.mgmt.edel.host| 10.10.0.2       | A    |
| nas.srv.edel.host         | 10.20.0.10      | A    |
| plex.srv.edel.host        | 10.20.0.11      | A    |
| files.edel.host           | 10.20.0.10      | CNAME|

#### 3. DHCP Integration

Configure DHCP to:
- Register client hostnames in DNS automatically
- Assign DNS suffixes based on VLAN membership
- Use consistent naming pattern for DHCP reservations

### Implementation Steps

1. **Preparation**
   - Document all existing DNS records
   - Create mapping of devices to new naming scheme
   - Identify public-facing services

2. **Cloudflare Configuration**
   - Update Cloudflare with only external-facing records
   - Configure security settings
   - Validate DNSSEC configuration

3. **UDMP Max Configuration**
   - Set up local DNS zone for `edel.host`
   - Create records for infrastructure devices
   - Configure DHCP to register client hostnames

4. **Testing**
   - Validate internal name resolution across all VLANs
   - Test external accessibility of public services
   - Verify split DNS is working correctly

5. **Documentation Update**
   - Document final naming scheme
   - Create DNS record inventory
   - Update network diagrams

### Maintenance Procedures

1. **Regular Audits**
   - Quarterly review of DNS records
   - Validation of internal/external resolution
   - Security assessment of exposed services

2. **Change Management**
   - Process for adding new devices to DNS
   - Procedure for exposing new services externally
   - Documentation requirements for DNS changes

3. **Backup Strategy**
   - Regular export of DNS configuration
   - Backup of Cloudflare DNS settings
   - Recovery procedure documentation

---

*This document should be updated as changes are made to the DNS infrastructure.*

Last Updated: March 5, 2025
