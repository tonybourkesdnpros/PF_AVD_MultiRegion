# RR3

## Table of Contents

- [Management](#management)
  - [Agents](#agents)
  - [Management API HTTP](#management-api-http)
- [Management Security](#management-security)
  - [Management Security Summary](#management-security-summary)
  - [Management Security SSL Profiles](#management-security-ssl-profiles)
  - [SSL profile STUN-DTLS Certificates Summary](#ssl-profile-stun-dtls-certificates-summary)
  - [Management Security Device Configuration](#management-security-device-configuration)
- [Monitoring](#monitoring)
  - [Flow Tracking](#flow-tracking)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [IP Security](#ip-security)
  - [IKE policies](#ike-policies)
  - [Security Association policies](#security-association-policies)
  - [IPSec profiles](#ipsec-profiles)
  - [IP Security Device Configuration](#ip-security-device-configuration)
- [Interfaces](#interfaces)
  - [DPS Interfaces](#dps-interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
  - [VXLAN Interface](#vxlan-interface)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Router Adaptive Virtual Topology](#router-adaptive-virtual-topology)
  - [Router Traffic-Engineering](#router-traffic-engineering)
  - [Router BGP](#router-bgp)
- [BFD](#bfd)
  - [Router BFD](#router-bfd)
- [Filters](#filters)
  - [Prefix-lists](#prefix-lists)
  - [Route-maps](#route-maps)
  - [IP Extended Community Lists](#ip-extended-community-lists)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [Platform](#platform)
  - [Platform Summary](#platform-summary)
  - [Platform Device Configuration](#platform-device-configuration)
- [Application Traffic Recognition](#application-traffic-recognition)
  - [Applications](#applications)
  - [Application Profiles](#application-profiles)
  - [Field Sets](#field-sets)
  - [Router Application-Traffic-Recognition Device Configuration](#router-application-traffic-recognition-device-configuration)
  - [Router Path-selection](#router-path-selection)
- [STUN](#stun)
  - [STUN Server](#stun-server)
  - [STUN Device Configuration](#stun-device-configuration)

## Management

### Agents

#### Agent KernelFib

##### Environment Variables

| Name | Value |
| ---- | ----- |
| KERNELFIB_PROGRAM_ALL_ECMP | 1 |

#### Agents Device Configuration

```eos
!
agent KernelFib environment KERNELFIB_PROGRAM_ALL_ECMP=1
```

### Management API HTTP

#### Management API HTTP Summary

| HTTP | HTTPS | Default Services |
| ---- | ----- | ---------------- |
| False | True | - |

#### Management API VRF Access

| VRF Name | IPv4 ACL | IPv6 ACL |
| -------- | -------- | -------- |
| MGMT | - | - |

#### Management API HTTP Device Configuration

```eos
!
management api http-commands
   protocol https
   no shutdown
   !
   vrf MGMT
      no shutdown
```

## Management Security

### Management Security Summary

| Settings | Value |
| -------- | ----- |

### Management Security SSL Profiles

| SSL Profile Name | TLS protocol accepted | Certificate filename | Key filename | Cipher List | CRLs |
| ---------------- | --------------------- | -------------------- | ------------ | ----------- | ---- |
| STUN-DTLS | 1.2 | STUN-DTLS.crt | STUN-DTLS.key | - | - |

### SSL profile STUN-DTLS Certificates Summary

| Trust Certificates | Requirement | Policy | System |
| ------------------ | ----------- | ------ | ------ |
| aristaDeviceCertProvisionerDefaultRootCA.crt | - | - | - |

### Management Security Device Configuration

```eos
!
management security
   ssl profile STUN-DTLS
      tls versions 1.2
      trust certificate aristaDeviceCertProvisionerDefaultRootCA.crt
      certificate STUN-DTLS.crt key STUN-DTLS.key
```

## Monitoring

### Flow Tracking

#### Flow Tracking Hardware

##### Trackers Summary

| Tracker Name | Record Export On Inactive Timeout | Record Export On Interval | Number of Exporters | Applied On |
| ------------ | --------------------------------- | ------------------------- | ------------------- | ---------- |
| FLOW-TRACKER | 70000 | 300000 | 1 | Dps1 |

##### Exporters Summary

| Tracker Name | Exporter Name | Collector IP/Host | Collector Port | Local Interface |
| ------------ | ------------- | ----------------- | -------------- | --------------- |
| FLOW-TRACKER | CV-TELEMETRY | - | - | Loopback0 |

#### Flow Tracking Device Configuration

```eos
!
flow tracking hardware
   tracker FLOW-TRACKER
      record export on inactive timeout 70000
      record export on interval 300000
      exporter CV-TELEMETRY
         collector 127.0.0.1
         local interface Loopback0
         template interval 3600000
   no shutdown
```

## Spanning Tree

### Spanning Tree Summary

STP mode: **none**

### Spanning Tree Device Configuration

```eos
!
spanning-tree mode none
```

## IP Security

### IKE policies

| Policy name | IKE lifetime | Encryption | DH group | Local ID |
| ----------- | ------------ | ---------- | -------- | -------- |
| CP-IKE-POLICY | - | - | - | 10.99.102.3 |

### Security Association policies

| Policy name | ESP Integrity | ESP Encryption | Lifetime | PFS DH Group |
| ----------- | ------------- | -------------- | -------- | ------------ |
| CP-SA-POLICY | - | aes256gcm128 | - | 14 |

### IPSec profiles

| Profile name | IKE policy | SA policy | Connection | DPD Interval | DPD Time | DPD action | Mode | Flow Parallelization |
| ------------ | ---------- | ----------| ---------- | ------------ | -------- | ---------- | ---- | -------------------- |
| CP-PROFILE | CP-IKE-POLICY | CP-SA-POLICY | start | - | - | - | transport | - |

### IP Security Device Configuration

```eos
!
ip security
   !
   ike policy CP-IKE-POLICY
      local-id 10.99.102.3
   !
   sa policy CP-SA-POLICY
      esp encryption aes256gcm128
      pfs dh-group 14
   !
   profile CP-PROFILE
      ike-policy CP-IKE-POLICY
      sa-policy CP-SA-POLICY
      connection start
      shared-key 7 <removed>
      dpd 10 50 clear
      mode transport
```

## Interfaces

### DPS Interfaces

#### DPS Interfaces Summary

| Interface | IP address | Shutdown | MTU | Flow tracker(s) | TCP MSS Ceiling |
| --------- | ---------- | -------- | --- | --------------- | --------------- |
| Dps1 | 10.99.102.3/32 | - | 9214 | Hardware: FLOW-TRACKER |  |

#### DPS Interfaces Device Configuration

```eos
!
interface Dps1
   description DPS Interface
   mtu 9214
   flow tracker hardware FLOW-TRACKER
   ip address 10.99.102.3/32
```

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet2 | mpls2 | routed | - | 192.25.76.2/24 | default | - | False | - | - |
| Ethernet3 | internet2 | routed | - | 192.26.76.2/24 | default | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet2
   description mpls2
   no shutdown
   no switchport
   ip address 192.25.76.2/24
!
interface Ethernet3
   description internet2
   no shutdown
   no switchport
   ip address 192.26.76.2/24
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Router_ID | default | 10.99.101.3/32 |

##### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | Router_ID | default | - |

#### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   description Router_ID
   no shutdown
   ip address 10.99.101.3/32
```

### VXLAN Interface

#### VXLAN Interface Summary

| Setting | Value |
| ------- | ----- |
| Source Interface | Dps1 |
| UDP port | 4789 |

##### VRF to VNI and Multicast Group Mappings

| VRF | VNI | Multicast Group |
| ---- | --- | --------------- |
| default | 1 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description RR3_VTEP
   vxlan source-interface Dps1
   vxlan udp-port 4789
   vxlan vrf default vni 1
```

## Routing

### Service Routing Protocols Model

Multi agent routing protocol model enabled

```eos
!
service routing protocols model multi-agent
```

### IP Routing

#### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | True |
| MGMT | False |

#### IP Routing Device Configuration

```eos
!
ip routing
no ip routing vrf MGMT
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| MGMT | false |

### Router Adaptive Virtual Topology

#### Router Adaptive Virtual Topology Summary

Topology role: pathfinder

#### AVT Profiles

| Profile name | Load balance policy | Internet exit policy |
| ------------ | ------------------- | -------------------- |
| DEFAULT-POLICY-CONTROL-PLANE | LB-DEFAULT-POLICY-CONTROL-PLANE | - |
| DEFAULT-POLICY-DEFAULT | LB-DEFAULT-POLICY-DEFAULT | - |

#### AVT Policies

##### AVT policy DEFAULT-POLICY-WITH-CP

| Application profile | AVT Profile | Traffic Class | DSCP |
| ------------------- | ----------- | ------------- | ---- |
| APP-PROFILE-CONTROL-PLANE | DEFAULT-POLICY-CONTROL-PLANE | - | - |
| default | DEFAULT-POLICY-DEFAULT | - | - |

#### VRFs configuration

##### VRF default

| AVT policy |
| ---------- |
| DEFAULT-POLICY-WITH-CP |

| AVT Profile | AVT ID |
| ----------- | ------ |
| DEFAULT-POLICY-DEFAULT | 1 |
| DEFAULT-POLICY-CONTROL-PLANE | 254 |

#### Router Adaptive Virtual Topology Configuration

```eos
!
router adaptive-virtual-topology
   topology role pathfinder
   !
   policy DEFAULT-POLICY-WITH-CP
      !
      match application-profile APP-PROFILE-CONTROL-PLANE
         avt profile DEFAULT-POLICY-CONTROL-PLANE
      !
      match application-profile default
         avt profile DEFAULT-POLICY-DEFAULT
   !
   profile DEFAULT-POLICY-CONTROL-PLANE
      path-selection load-balance LB-DEFAULT-POLICY-CONTROL-PLANE
   !
   profile DEFAULT-POLICY-DEFAULT
      path-selection load-balance LB-DEFAULT-POLICY-DEFAULT
   !
   vrf default
      avt policy DEFAULT-POLICY-WITH-CP
      avt profile DEFAULT-POLICY-DEFAULT id 1
      avt profile DEFAULT-POLICY-CONTROL-PLANE id 254
```

### Router Traffic-Engineering

- Traffic Engineering is enabled.

#### Router Traffic Engineering Device Configuration

```eos
!
router traffic-engineering
```

### Router BGP

ASN Notation: asplain

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65000 | 10.99.101.3 |

| BGP AS | Cluster ID |
| ------ | --------- |
| 65000 | 10.99.101.3 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 16 |

#### Router BGP Listen Ranges

| Prefix | Peer-ID Include Router ID | Peer Group | Peer-Filter | Remote-AS | VRF |
| ------ | ------------------------- | ---------- | ----------- | --------- | --- |
| 10.99.101.0/24 | - | WAN-OVERLAY-PEERS | - | 65000 | default |
| 10.99.102.0/24 | - | WAN-OVERLAY-PEERS | - | 65000 | default |
| 10.99.201.0/24 | - | WAN-OVERLAY-PEERS | - | 65000 | default |
| 10.99.202.0/24 | - | WAN-OVERLAY-PEERS | - | 65000 | default |

#### Router BGP Peer Groups

##### WAN-OVERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | wan |
| Remote AS | 65000 |
| Route Reflector Client | Yes |
| Source | Dps1 |
| BFD | True |
| BFD Timers | interval: 1000, min_rx: 1000, multiplier: 10 |
| TTL Max Hops | 1 |
| Send community | all |
| Maximum routes | 0 (no limit) |

#### Router BGP EVPN Address Family

- Next-hop resolution is **disabled**

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| WAN-OVERLAY-PEERS | True | default |

#### Router BGP IPv4 SR-TE Address Family

##### IPv4 SR-TE Peer Groups

| Peer Group | Activate | Route-map In | Route-map Out |
| ---------- | -------- | ------------ | ------------- |
| WAN-OVERLAY-PEERS | True | - | - |

#### Router BGP Link-State Address Family

##### Link-State Peer Groups

| Peer Group | Activate | Missing policy In action | Missing policy Out action |
| ---------- | -------- | ------------------------ | ------------------------- |
| WAN-OVERLAY-PEERS | True | - | deny |

##### Link-State Path Selection Configuration

| Settings | Value |
| -------- | ----- |
| Role(s) | consumer<br>propagator |

#### Router BGP Path-Selection Address Family

##### Path-Selection Peer Groups

| Peer Group | Activate |
| ---------- | -------- |
| WAN-OVERLAY-PEERS | True |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| default | 10.99.101.3:1 | - |

#### Router BGP Device Configuration

```eos
!
router bgp 65000
   router-id 10.99.101.3
   maximum-paths 16
   no bgp default ipv4-unicast
   bgp cluster-id 10.99.101.3
   bgp listen range 10.99.101.0/24 peer-group WAN-OVERLAY-PEERS remote-as 65000
   bgp listen range 10.99.102.0/24 peer-group WAN-OVERLAY-PEERS remote-as 65000
   bgp listen range 10.99.201.0/24 peer-group WAN-OVERLAY-PEERS remote-as 65000
   bgp listen range 10.99.202.0/24 peer-group WAN-OVERLAY-PEERS remote-as 65000
   neighbor WAN-OVERLAY-PEERS peer group
   neighbor WAN-OVERLAY-PEERS remote-as 65000
   neighbor WAN-OVERLAY-PEERS update-source Dps1
   neighbor WAN-OVERLAY-PEERS route-reflector-client
   neighbor WAN-OVERLAY-PEERS bfd
   neighbor WAN-OVERLAY-PEERS bfd interval 1000 min-rx 1000 multiplier 10
   neighbor WAN-OVERLAY-PEERS ttl maximum-hops 1
   neighbor WAN-OVERLAY-PEERS password 7 <removed>
   neighbor WAN-OVERLAY-PEERS send-community
   neighbor WAN-OVERLAY-PEERS maximum-routes 0
   redistribute connected route-map RM-CONN-2-BGP
   !
   address-family evpn
      neighbor WAN-OVERLAY-PEERS activate
      next-hop resolution disabled
   !
   address-family ipv4
      no neighbor WAN-OVERLAY-PEERS activate
   !
   address-family ipv4 sr-te
      neighbor WAN-OVERLAY-PEERS activate
   !
   address-family link-state
      neighbor WAN-OVERLAY-PEERS activate
      neighbor WAN-OVERLAY-PEERS missing-policy direction out action deny
      path-selection role consumer propagator
   !
   address-family path-selection
      bgp additional-paths receive
      bgp additional-paths send any
      neighbor WAN-OVERLAY-PEERS activate
   !
   vrf default
      rd 10.99.101.3:1
      route-target import evpn 1:1
      route-target export evpn 1:1
      route-target export evpn route-map RM-EVPN-EXPORT-VRF-DEFAULT
```

## BFD

### Router BFD

#### Router BFD Multihop Summary

| Interval | Minimum RX | Multiplier |
| -------- | ---------- | ---------- |
| 300 | 300 | 3 |

#### Router BFD Device Configuration

```eos
!
router bfd
   multihop interval 300 min-rx 300 multiplier 3
```

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-LOOPBACKS-EVPN-OVERLAY

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.99.101.0/24 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.99.101.0/24 eq 32
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | extcommunity soo 10.99.101.3:0 additive | - | - |

##### RM-EVPN-EXPORT-VRF-DEFAULT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | extcommunity ECL-EVPN-SOO | - | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   set extcommunity soo 10.99.101.3:0 additive
!
route-map RM-EVPN-EXPORT-VRF-DEFAULT permit 10
   match extcommunity ECL-EVPN-SOO
```

### IP Extended Community Lists

#### IP Extended Community Lists Summary

| List Name | Type | Extended Communities |
| --------- | ---- | -------------------- |
| ECL-EVPN-SOO | permit | soo 10.99.101.3:0 |

#### IP Extended Community Lists Device Configuration

```eos
!
ip extcommunity-list ECL-EVPN-SOO permit soo 10.99.101.3:0
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| MGMT | disabled |

### VRF Instances Device Configuration

```eos
!
vrf instance MGMT
```

## Platform

### Platform Summary

#### Platform Software Forwarding Engine Summary

| Settings | Value |
| -------- | ----- |
| Maximum CPU Allocation | 1 |

### Platform Device Configuration

```eos
!
platform sfe data-plane cpu allocation maximum 1
```

## Application Traffic Recognition

### Applications

#### IPv4 Applications

| Name | Source Prefix | Destination Prefix | Protocols | Protocol Ranges | TCP Source Port Set | TCP Destination Port Set | UDP Source Port Set | UDP Destination Port Set | DSCP |
| ---- | ------------- | ------------------ | --------- | --------------- | ------------------- | ------------------------ | ------------------- | ------------------------ | ---- |
| APP-CONTROL-PLANE | PFX-LOCAL-VTEP-IP | - | - | - | - | - | - | - | - |

### Application Profiles

#### Application Profile Name APP-PROFILE-CONTROL-PLANE

| Type | Name | Service |
| ---- | ---- | ------- |
| application | APP-CONTROL-PLANE | - |

### Field Sets

#### IPv4 Prefix Sets

| Name | Prefixes |
| ---- | -------- |
| PFX-LOCAL-VTEP-IP | 10.99.102.3/32 |

### Router Application-Traffic-Recognition Device Configuration

```eos
!
application traffic recognition
   !
   application ipv4 APP-CONTROL-PLANE
      source prefix field-set PFX-LOCAL-VTEP-IP
   !
   application-profile APP-PROFILE-CONTROL-PLANE
      application APP-CONTROL-PLANE
   !
   field-set ipv4 prefix PFX-LOCAL-VTEP-IP
      10.99.102.3/32
```

### Router Path-selection

#### Router Path-selection Summary

| Setting | Value |
| ------  | ----- |
| Dynamic peers source | STUN |

#### TCP MSS Ceiling Configuration

| IPV4 segment size | Direction |
| ----------------- | --------- |
| auto | ingress |

#### Path Groups

##### Path Group internet_path

| Setting | Value |
| ------  | ----- |
| Path Group ID | 1002 |

##### Path Group internet_path2

| Setting | Value |
| ------  | ----- |
| Path Group ID | 1006 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet3 | - |  |

##### Path Group LAN_HA

| Setting | Value |
| ------  | ----- |
| Path Group ID | 65535 |
| Flow assignment | LAN |

##### Path Group mpls_path

| Setting | Value |
| ------  | ----- |
| Path Group ID | 1001 |

##### Path Group mpls_path2

| Setting | Value |
| ------  | ----- |
| Path Group ID | 1005 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet2 | - |  |

#### Load-balance Policies

| Policy Name | Jitter (ms) | Latency (ms) | Loss Rate (%) | Path Groups (priority) | Lowest Hop Count |
| ----------- | ----------- | ------------ | ------------- | ---------------------- | ---------------- |
| LB-DEFAULT-POLICY-CONTROL-PLANE | - | - | - | internet_path (1)<br>internet_path2 (1)<br>LAN_HA (1)<br>mpls_path (1)<br>mpls_path2 (1) | False |
| LB-DEFAULT-POLICY-DEFAULT | - | - | - | internet_path (1)<br>internet_path2 (1)<br>LAN_HA (1)<br>mpls_path (1)<br>mpls_path2 (1) | False |

#### Router Path-selection Device Configuration

```eos
!
router path-selection
   peer dynamic source stun
   tcp mss ceiling ipv4 ingress
   !
   path-group internet_path id 1002
   !
   path-group internet_path2 id 1006
      ipsec profile CP-PROFILE
      !
      local interface Ethernet3
   !
   path-group LAN_HA id 65535
      flow assignment lan
   !
   path-group mpls_path id 1001
   !
   path-group mpls_path2 id 1005
      ipsec profile CP-PROFILE
      !
      local interface Ethernet2
   !
   load-balance policy LB-DEFAULT-POLICY-CONTROL-PLANE
      path-group internet_path
      path-group internet_path2
      path-group LAN_HA
      path-group mpls_path
      path-group mpls_path2
   !
   load-balance policy LB-DEFAULT-POLICY-DEFAULT
      path-group internet_path
      path-group internet_path2
      path-group LAN_HA
      path-group mpls_path
      path-group mpls_path2
```

## STUN

### STUN Server

| Server Local Interfaces | Bindings Timeout (s) | SSL Profile | SSL Connection Lifetime | Port |
| ----------------------- | -------------------- | ----------- | ----------------------- | ---- |
| Ethernet2<br>Ethernet3 | - | STUN-DTLS | - | 3478 |

### STUN Device Configuration

```eos
!
stun
   server
      local-interface Ethernet2
      local-interface Ethernet3
      ssl profile STUN-DTLS
```
