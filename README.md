# Large-Scale Three-Tier Hybrid Enterprise Network Architecture

This repository contains the design, documentation, and configuration files for a robust, high-availability Enterprise Network Architecture simulated inside GNS3. The network features a fully redundant **Three-Tier Hierarchical Design** coupled with a data-center-style **Spine-Leaf Architecture** for dedicated server infrastructure.

---

## 🗺️ Network Topology Overview
Below is the architectural blueprint of the enterprise infrastructure, highlighting the core ring, distributed user blocks, and the isolated server farm:

<img width="866" height="722" alt="image" src="https://github.com/user-attachments/assets/dce14192-1c55-4b6f-a0fe-e9331423841f" />



---

## 🛠️ Key Architectural Pillars & Protocols

### 1. Three-Tier Hierarchical Design
*   **Core Layer:** Engineered for high-speed packet transit utilizing an outer core ring (R1-R8) integrated with a central hub router (R9) acting as the main routing cross-connect.
*   **Distribution Layer:** Implements intelligent routing policies and boundary points using high-performance Multi-Layer Switches (MUL1 to MUL8).
*   **Access Layer:** Attaches end-user VPCS devices and servers directly to the network through dedicated Layer 2 access switches, segmenting traffic efficiently.

### 2. Implemented Protocols & Technologies
*   **Dynamic Routing (OSPFv2):** Configured as the core IGP spanning `Area 0` across all 9 routers and Multilayer switches to guarantee highly scalable dynamic route advertisement and rapid convergence.
*   **First Hop Redundancy (HSRP):** Configured on the Multi-Layer distribution pairs to provide high-availability default virtual gateways for seamless network failover.
*   **Link Aggregation (LACP / EtherChannel):** Active Port-Channels configured between interconnected distribution switches to scale uplinks and eliminate potential single points of failure.
*   **VLAN Segmentation & 802.1Q Trunking:** Network broadcast domains are optimized into specific VLANs:
    *   **VLAN 10:** User Subnet (10.0.10.0/24)
    *   **VLAN 20:** User Subnet (10.1.10.0/24)
    *   **VLAN 30:** User Subnet (10.2.10.0/24)
    *   **Server Network:** Isolated High-Performance Server Leaf Subnets (10.3.x.x)
*   **Inter-VLAN Routing (SVI):** Enabled on distribution layers to manage fast routing between distinct VLAN blocks and the upper server farm.

---

## 📊 Comprehensive IP Addressing & Mapping Reference

### 🔹 Layer 3 Core Infrastructure Routing (200.100.10.0/30 Networks)

#### Outer Core Ring Connections
| Table Ref | Connected Devices | Device A Port | Device B Port | Device A IP/Mask | Device B IP/Mask |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Table 1** | R1 ↔ R2 | G3/0 | G3/0 | 200.100.10.1/30 | 200.100.10.2/30 |
| **Table 2** | R3 ↔ R4 | G3/0 | G3/0 | 200.100.10.5/30 | 200.100.10.6/30 |
| **Table 3** | R5 ↔ R6 | G3/0 | G3/0 | 200.100.10.9/30 | 200.100.10.10/30 |
| **Table 4** | R7 ↔ R8 | G3/0 | G3/0 | 200.100.10.13/30 | 200.100.10.14/30 |

#### Central Hub Core Node (R9) Star Interconnections
| Table Ref | Connected Links | Device A Port | Device B Port | Device A IP/Mask | Device B IP/Mask |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Table 5** | R1 ↔ R9 | G6/0 | E1/0 | 200.100.10.18/30 | 200.100.10.17/30 |
| **Table 5** | R2 ↔ R9 | G6/0 | E1/1 | 200.100.10.21/30 | 200.100.10.22/30 |
| **Table 5** | R3 ↔ R9 | G6/0 | E1/2 | 200.100.10.25/30 | 200.100.10.26/30 |
| **Table 5** | R4 ↔ R9 | G6/0 | E1/3 | 200.100.10.30/30 | 200.100.10.29/30 |
| **Table 5** | R5 ↔ R9 | G6/0 | E1/4 | 200.100.10.34/30 | 200.100.10.33/30 |
| **Table 5** | R6 ↔ R9 | G6/0 | E1/5 | 200.100.10.38/30 | 200.100.10.37/30 |
| **Table 5** | R7 ↔ R9 | G6/0 | E1/6 | 200.100.10.42/30 | 200.100.10.41/30 |
| **Table 5** | R8 ↔ R9 | G6/0 | E1/7 | 200.100.10.46/30 | 200.100.10.45/30 |

---

### 🔹 Layer 3 Distribution Blocks

#### VLAN 10 Block (MUL1 & MUL2 Pair)
| Connected Link | Device A Port | Device B Port | Device A IP/Mask | Device B IP/Mask |
| :--- | :---: | :---: | :---: | :---: |
| **MUL1 ↔ R1** | G1/0 | G1/0 | 10.0.100.2/30 | 10.0.100.1/30 |
| **MUL1 ↔ R2** | G2/0 | G1/1 | 10.0.250.2/30 | 10.0.250.1/30 |
| **MUL2 ↔ R1** | G2/0 | G1/1 | 10.0.150.2/30 | 10.0.150.1/30 |
| **MUL2 ↔ R2** | G1/0 | G1/0 | 10.0.200.2/30 | 10.0.200.1/30 |
| **LACP SVI Routing** | Port-Channel 1 | (MUL1 SVI) `10.0.10.2` | Port-Channel 1 | (MUL2 SVI) `10.0.10.3` |

#### VLAN 20 Block (MUL3 & MUL4 Pair)
| Connected Link | Device A Port | Device B Port | Device A IP/Mask | Device B IP/Mask |
| :--- | :---: | :---: | :---: | :---: |
| **MUL3 ↔ R3** | G1/0 | G1/0 | 10.1.100.2/30 | 10.1.100.1/30 |
| **MUL3 ↔ R4** | G1/1 | G2/0 | 10.1.250.2/30 | 10.1.250.1/30 |
| **MUL4 ↔ R3** | G1/1 | G2/0 | 10.1.150.2/30 | 10.1.150.1/30 |
| **MUL4 ↔ R4** | G1/0 | G1/0 | 10.1.200.1/30 | 10.1.200.2/30 |
| **LACP SVI Routing** | Port-Channel 1 | (MUL3 SVI) `10.1.10.2` | Port-Channel 1 | (MUL4 SVI) `10.1.10.3` |

#### VLAN 30 Block (MUL5 & MUL6 Pair)
| Connected Link | Device A Port | Device B Port | Device A IP/Mask | Device B IP/Mask |
| :--- | :---: | :---: | :---: | :---: |
| **MUL5 ↔ R5** | G1/0 | G1/0 | 10.2.100.1/30 | 10.2.100.2/30 |
| **MUL5 ↔ R6** | G2/0 | G1/1 | 10.2.250.2/30 | 10.2.250.1/30 |
| **MUL6 ↔ R5** | G1/1 | G2/0 | 10.2.150.2/30 | 10.2.150.1/30 |
| **MUL6 ↔ R6** | G1/0 | G1/0 | 10.2.200.2/30 | 10.2.200.1/30 |
| **LACP SVI Routing** | Port-Channel 1 | (MUL5 SVI) `10.2.10.2` | Port-Channel 1 | (MUL6 SVI) `10.2.10.3` |

#### Datacenter Spine-Leaf Infrastructure Block (MUL7 & MUL8 Pair)
| Connected Link | Device A Port | Device B Port | Device A IP/Mask | Device B IP/Mask |
| :--- | :---: | :---: | :---: | :---: |
| **MUL7 ↔ R7** | G0/0 | G1/0 | 10.3.100.2/30 | 10.3.100.1/30 |
| **MUL7 ↔ R8** | G0/1 | G2/0 | 10.3.150.2/30 | 10.3.150.1/30 |
| **MUL8 ↔ R7** | G0/1 | G2/0 | 10.3.250.2/30 | 10.3.250.1/30 |
| **MUL8 ↔ R8** | G0/0 | G1/0 | 10.3.200.2/30 | 10.3.200.1/30 |
| **MUL7 Server SVIs** | VLAN 10 SVI: `10.3.10.2` | VLAN 20 SVI: `10.3.20.2` | VLAN 30 SVI: `10.3.30.2` |
| **MUL8 Server SVIs** | VLAN 10 SVI: `10.3.10.3` | VLAN 20 SVI: `10.3.20.3` | VLAN 30 SVI: `10.3.30.3` |

---

### 🔹 End Devices & Datacenter Server Addressing Table
| Device Name | Connected Access Switch | Active Network VLAN | Assigned IP Address | Subnet Mask | Configured Default Gateway |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **PC1** | Sw1 | VLAN 10 | 10.0.10.11 | 255.255.255.0 | 10.0.10.1 (HSRP Virtual IP) |
| **PC2** | Sw1 | VLAN 10 | 10.0.10.12 | 255.255.255.0 | 10.0.10.1 (HSRP Virtual IP) |
| **PC3** | Sw2 | VLAN 10 | 10.0.10.13 | 255.255.255.0 | 10.0.10.1 (HSRP Virtual IP) |
| **PC4** | Sw2 | VLAN 10 | 10.0.10.14 | 255.255.255.0 | 10.0.10.1 (HSRP Virtual IP) |
| **PC5** | Sw3 | VLAN 20 | 10.1.10.11 | 255.255.255.0 | 10.1.10.1 (HSRP Virtual IP) |
| **PC6** | Sw3 | VLAN 20 | 10.1.10.12 | 255.255.255.0 | 10.1.10.1 (HSRP Virtual IP) |
| **PC7** | Sw4 | VLAN 20 | 10.1.10.13 | 255.255.255.0 | 10.1.10.1 (HSRP Virtual IP) |
| **PC8** | Sw4 | VLAN 20 | 10.1.10.14 | 255.255.255.0 | 10.1.10.1 (HSRP Virtual IP) |
| **PC9** | Sw5 | VLAN 30 | 10.2.10.11 | 255.255.255.0 | 10.2.10.1 (HSRP Virtual IP) |
| **PC11** | Sw5 | VLAN 30 | 10.2.10.12 | 255.255.255.0 | 10.2.10.1 (HSRP Virtual IP) |
| **PC10** | Sw6 | VLAN 30 | 10.2.10.13 | 255.255.255.0 | 10.2.10.1 (HSRP Virtual IP) |
| **PC12** | Sw6 | VLAN 30 | 10.2.10.14 | 255.255.255.0 | 10.2.10.1 (HSRP Virtual IP) |
| **Server10** | SW-VLAN10 | Server Net | 10.3.10.10 | 255.255.255.255 | 10.3.10.2 / 10.3.10.3 (Leaf SVI Pair) |
| **Server20** | SW-VLAN20 | Server Net | 10.3.20.10 | 255.255.255.255 | 10.3.20.2 / 10.3.20.3 (Leaf SVI Pair) |
| **Server30** | SW-VLAN30 | Server Net | 10.3.30.10 | 255.255.255.255 | 10.3.30.2 / 10.3.30.3 (Leaf SVI Pair) |

---

## 🚀 Verification & Deliverables Status
- [x] Comprehensive /30 Core Network Subnet Planning.
- [x] LACP Multi-chassis EtherChannel configuration.
- [x] Multi-Area OSPFv2 full neighbor convergence verified via central core node R9.
- [x] Dynamic HSRP high-availability routing testing.
- [x] End-to-end full connectivity ping tests verified from all local PC nodes to target Data Center servers.
