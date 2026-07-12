# Large-Scale Three-Tier Hybrid Enterprise Network Architecture

This repository contains the design, documentation, and configuration files for a robust, high-availability Enterprise Network Architecture simulated inside GNS3. The network features a fully redundant **Three-Tier Hierarchical Design** coupled with a data-center-style **Spine-Leaf Architecture** for dedicated server infrastructure.

---

##  Network Topology Overview
Below is the architectural blueprint of the enterprise infrastructure, highlighting the core ring, distributed user blocks, and the isolated server farm:

<img width="866" height="722" alt="image" src="https://github.com/user-attachments/assets/05353617-1fb3-45d2-83b3-d1a0fbcda702" />


---

## 🛠️ Key Architectural Pillars & Protocols

### 1. Three-Tier Hierarchical Design
*   **Core Layer:** Engineered for high-speed packet transit utilizing an outer core ring (R1-R8) integrated with a central hub router (R9) acting as the main routing cross-connect.
*   **Distribution Layer:** Implements intelligent routing policies and boundary points using high-performance Multi-Layer Switches (MUL1 to MUL8).
*   **Access Layer:** Attaches end-user VPCS devices and servers directly to the network through dedicated Layer 2 access switches, segmenting traffic efficiently.

### 2. Network Security & Dynamic Addressing Configuration
*   **Data Center Traffic Enforcement (Extended ACL):** Hardened security on the Spine-Leaf infrastructure (MUL7 & MUL8) by deploying an Extended IP Access Control List (`DATA_CENTER_SECURITY`). This ensures strict, deterministic traffic matching:
    *   `VLAN 10` blocks (`10.0.0.0/16`) are permitted exclusively to `Server Net 10.3.10.0/24`.
    *   `VLAN 20` blocks (`10.1.0.0/16`) are permitted exclusively to `Server Net 10.3.20.0/24`.
    *   `VLAN 30` blocks (`10.2.0.0/16`) are permitted exclusively to `Server Net 10.3.30.0/24`.
    *   All unmapped cross-traffic is dropped implicitly to isolate server environments.
*   **Centralized Dynamic Addressing (DHCP Server):** Dedicated DHCP IP pools are configured locally on every branch router (R1 through R8, excluding central transit node R9) to operate as the default DHCP Server, dynamically provisioning network IP parameters for each corresponding local area network (LAN).

### 3. Core Protocols & Technologies
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

### Table 1: Point-to-Point and IP addressing between R1 and R2
| Connected | Port | IP/Mask |
| :--- | :---: | :---: |
| **R1** | G3/0 | 200.100.10.1/30 |
| **R2** | G3/0 | 200.100.10.2/30 |

### Table 2: Point-to-Point and IP addressing between R3 and R4
| Connected | Port | IP/Mask |
| :--- | :---: | :---: |
| **R3** | G3/0 | 200.100.10.5/30 |
| **R4** | G3/0 | 200.100.10.6/30 |

### Table 3: Point-to-Point and IP addressing between R5 and R6
| Connected | Port | IP/Mask |
| :--- | :---: | :---: |
| **R5** | G3/0 | 200.100.10.9/30 |
| **R6** | G3/0 | 200.100.10.10/30 |

### Table 4: Point-to-Point and IP addressing between R7 and R8
| Connected | Port | IP/Mask |
| :--- | :---: | :---: |
| **R8** | G3/0 | 200.100.10.13/30 |
| **R7** | G3/0 | 200.100.10.14/30 |

### Table 5: Point-to-Point and IP addressing between R9 and All Core Routers
| Connected | Port | IP/Mask |
| :--- | :---: | :---: |
| **R1** | G6/0 | 200.100.10.18/30 |
| **R9** | E1/0 | 200.100.10.17/30 |
| | | |
| **R2** | G6/0 | 200.100.10.21/30 |
| **R9** | E1/1 | 200.100.10.22/30 |
| | | |
| **R9** | E1/2 | 200.100.10.26/30 |
| **R3** | G6/0 | 200.100.10.25/30 |
| | | |
| **R4** | G6/0 | 200.100.10.30/30 |
| **R9** | E1/3 | 200.100.10.29/30 |
| | | |
| **R9** | G6/0 | 200.100.10.34/30 |
| **R5** | E1/4 | 200.100.10.33/30 |
| | | |
| **R9** | E1/5 | 200.100.10.38/30 |
| **R6** | G6/0 | 200.100.10.37/30 |
| | | |
| **R9** | G6/0 | 200.100.10.42/30 |
| **R7** | E1/6 | 200.100.10.41/30 |
| | | |
| **R8** | G6/0 | 200.100.10.46/30 |
| **R9** | E1/7 | 200.100.10.45/30 |

---

### 🔹 Layer 3 Distribution Blocks

#### VLAN 10 Block (MUL1 & MUL2 Pair)
| Connected | Port | IP/Mask |
| :--- | :---: | :---: |
| **MUL1** | G1/0 | 10.0.100.2/30 |
| **R1** | G1/0 | 10.0.100.1/30 |
| | | |
| **MUL1** | G2/0 | 10.0.250.2/30 |
| **R2** | G1/1 | 10.0.250.1/30 |
| | | |
| **MUL2** | G2/0 | 10.0.150.2/30 |
| **R1** | G1/1 | 10.0.150.1/30 |
| | | |
| **MUL2** | G1/0 | 10.0.200.2/30 |
| **R2** | G1/0 | 10.0.200.1/30 |
| | | |
| **MUL2** / **MUL1** | Port-Channel 1 (MUL1) | 10.0.10.2 (VLAN10) |
| **MUL2** / **MUL1** | Port-Channel 1 (MUL2) | 10.0.10.3 (VLAN10) |

#### VLAN 20 Block (MUL3 & MUL4 Pair)
| Connected | Port | IP/Mask |
| :--- | :---: | :---: |
| **MUL3** | G1/0 | 10.1.100.2/30 |
| **R3** | G1/0 | 10.1.100.1/30 |
| | | |
| **MUL3** | G1/1 | 10.1.250.2/30 |
| **R4** | G2/0 | 10.1.250.1/30 |
| | | |
| **MUL4** | G1/1 | 10.1.150.2/30 |
| **R3** | G2/0 | 10.1.150.1/30 |
| | | |
| **MUL4** | G1/0 | 10.1.200.1/30 |
| **R4** | G1/0 | 10.1.200.2/30 |
| | | |
| **MUL4** / **MUL3** | Port-Channel 1 (MUL3) | 10.1.10.2 (VLAN20) |
| **MUL4** / **MUL3** | Port-Channel 1 (MUL4) | 10.1.10.3 (VLAN20) |

#### VLAN 30 Block (MUL5 & MUL6 Pair)
| Connected | Port | IP/Mask |
| :--- | :---: | :---: |
| **MUL5** | G1/0 | 10.2.100.1/30 |
| **R5** | G1/0 | 10.2.100.2/30 |
| | | |
| **MUL5** | G2/0 | 10.2.250.2/30 |
| **R6** | G1/1 | 10.2.250.1/30 |
| | | |
| **MUL6** | G1/1 | 10.2.150.2/30 |
| **R5** | G2/0 | 10.2.150.1/30 |
| | | |
| **MUL6** | G1/0 | 10.2.200.2/30 |
| **R6** | G1/0 | 10.2.200.1/30 |
| | | |
| **MUL6** / **MUL5** | Port-Channel 1 (MUL5) | 10.2.10.2 (VLAN30) |
| **MUL6** / **MUL5** | Port-Channel 1 (MUL6) | 10.2.10.3 (VLAN30) |

#### Datacenter Spine-Leaf Infrastructure Block (MUL7 & MUL8 Pair)
| Connected | Port | IP/Mask |
| :--- | :---: | :---: |
| **MUL7** | G1/0 | 10.3.100.2/30 |
| **R7** | G0/0 | 10.3.100.1/30 |
| | | |
| **MUL7** | G0/1 | 10.3.150.2/30 |
| **R8** | G2/0 | 10.3.150.1/30 |
| | | |
| **MUL8** | G0/1 | 10.3.250.2/30 |
| **R7** | G2/0 | 10.3.250.1/30 |
| | | |
| **MUL8** | G0/0 | 10.3.200.2/30 |
| **R8** | G1/0 | 10.3.200.1/30 |

| Device Name | VLAN 10 SVI | VLAN 20 SVI | VLAN 30 SVI |
| :--- | :---: | :---: | :---: |
| **MUL7** | 10.3.10.2 | 10.3.20.2 | 10.3.30.2 |
| **MUL8** | 10.3.10.3 | 10.3.20.3 | 10.3.30.3 |

---

### 🔹 End Devices & Datacenter Server Addressing Table
| Device Name | Connected Switch | VLAN / Subnet Type | IP Address | Subnet Mask | Default Gateway Protocol / Logic |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **PC1** | Sw1 | VLAN 10 (DHCP Assigned) | 10.0.10.11 | 255.255.255.0 | 10.0.10.1 (HSRP Virtual IP) |
| **PC2** | Sw1 | VLAN 10 (DHCP Assigned) | 10.0.10.12 | 255.255.255.0 | 10.0.10.1 (HSRP Virtual IP) |
| **PC3** | Sw2 | VLAN 10 (DHCP Assigned) | 10.0.10.13 | 255.255.255.0 | 10.0.10.1 (HSRP Virtual IP) |
| **PC4** | Sw2 | VLAN 10 (DHCP Assigned) | 10.0.10.14 | 255.255.255.0 | 10.0.10.1 (HSRP Virtual IP) |
| **PC5** | Sw3 | VLAN 20 (DHCP Assigned) | 10.1.10.11 | 255.255.255.0 | 10.1.10.1 (HSRP Virtual IP) |
| **PC6** | Sw3 | VLAN 20 (DHCP Assigned) | 10.1.10.12 | 255.255.255.0 | 10.1.10.1 (HSRP Virtual IP) |
| **PC7** | Sw4 | VLAN 20 (DHCP Assigned) | 10.1.10.13 | 255.255.255.0 | 10.1.10.1 (HSRP Virtual IP) |
| **PC8** | Sw4 | VLAN 20 (DHCP Assigned) | 10.1.10.14 | 255.255.255.0 | 10.1.10.1 (HSRP Virtual IP) |
| **PC9** | Sw5 | VLAN 30 (DHCP Assigned) | 10.2.10.11 | 255.255.255.0 | 10.2.10.1 (HSRP Virtual IP) |
| **PC11** | Sw5 | VLAN 30 (DHCP Assigned) | 10.2.10.12 | 255.255.255.0 | 10.2.10.1 (HSRP Virtual IP) |
| **PC10** | Sw6 | VLAN 30 (DHCP Assigned) | 10.2.10.13 | 255.255.255.0 | 10.2.10.1 (HSRP Virtual IP) |
| **PC12** | Sw6 | VLAN 30 (DHCP Assigned) | 10.2.10.14 | 255.255.255.0 | 10.2.10.1 (HSRP Virtual IP) |
| **Server10** | SW-VLAN10 | Static Server Net | 10.3.10.10 | 255.255.255.255 | 10.3.10.2 / 10.3.10.3 (Secured via ACL) |
| **Server20** | SW-VLAN20 | Static Server Net | 10.3.20.10 | 255.255.255.255 | 10.3.20.2 / 10.3.20.3 (Secured via ACL) |
| **Server30** | SW-VLAN30 | Static Server Net | 10.3.30.10 | 255.255.255.255 | 10.3.30.2 / 10.3.30.3 (Secured via ACL) |

---
### Verification & Testing
The following tests demonstrate the network's functionality and security policies:

## **1. End-to-End Connectivity**
*Testing inter-VLAN routing by pinging and tracing from PC1 (VLAN 10) to PC8 (VLAN 20).*
<img width="1288" height="576" alt="image" src="https://github.com/user-attachments/assets/369fb569-05d8-41e2-a44f-392b4f56f743" />


## **2. Dynamic Routing (OSPFv2)**
*Verifying OSPF neighbor adjacencies and the dynamic routing table.*
* **OSPF Neighbors:**
<img width="1252" height="427" alt="image" src="https://github.com/user-attachments/assets/13f2c6c0-26a6-4c09-afad-c6e317b58894" />

* **OSPF Routes:**
* <img width="532" height="706" alt="image" src="https://github.com/user-attachments/assets/0813a138-d001-4cbf-853c-414190bb17fa" />


## **3. Gateway Redundancy (HSRP)**
*Confirming HSRP status for VLAN 10 to ensure high availability and automatic failover.*

* **HSRP Status:**
  *This output confirms the device is in 'Standby' state, with the active gateway identified as 10.0.10.2 and the virtual IP (VIP) successfully configured.*
  <img width="1183" height="182" alt="image" src="https://github.com/user-attachments/assets/f357d1f6-a75b-4e39-be5b-19d50813dc87" />


## **4. DHCP Service**
*Validating IP address assignment.*
* **Server/Switch Binding:** `show ip dhcp binding`
 <img width="1257" height="366" alt="image" src="https://github.com/user-attachments/assets/b669c981-22d5-429c-8b4a-c8f170d0463a" />

* **Client Configuration:** PC (VLAN 10) IP request success.
 <img width="568" height="152" alt="image" src="https://github.com/user-attachments/assets/445a3e8f-a263-4332-990a-883087611daf" />



## **5. ACL Security Testing (Spine-Leaf Layer)**
*Verifying security policies enforced at the Spine-Leaf layer to control traffic between segments.*

* **Permitted Access (PC1 VLAN 10 to Server 10):**
  *Connectivity is successfully established to the permitted server.*
 <img width="1797" height="577" alt="image" src="https://github.com/user-attachments/assets/b30edc19-74cf-4f2c-91b6-2f6a7c658232" />


* **Denied Access (PC1 VLAN 10 to Server 20):**
  *Access is actively rejected by the ACL configuration. The 'Communication administratively prohibited' message confirms the security policy is correctly applied and dropping unauthorized traffic.*
<img width="1797" height="577" alt="image" src="https://github.com/user-attachments/assets/d7315cc6-6746-4741-b1ac-828feb1cf692" />

  
##  How to Download & Run the Topology

Want to test and explore this network live? Follow these steps to run the simulation on your local machine:

### Prerequisites
* **GNS3** installed (Latest version recommended).
* To run this network, you need these appliances configured in your GNS3:

**Routers: Cisco c7200. Please ensure you have your own valid Cisco IOS image configured for this platform.**

**Multilayer & L2 Switch: QEMU VM. Please ensure you have the virtioa.qcow2 disk image imported as a QEMU template.**

### Execution Steps
1. **Download the Project File:**
   * ⬇️ ** [Large-Scale Three-Tier Hybrid Enterprise Network.zip](https://github.com/user-attachments/files/29941358/Large-Scale.Three-Tier.Hybrid.Enterprise.Network.zip) **
2. **Import into GNS3:** 
   * Launch GNS3, go to **File > Import portable project**, and select the downloaded `.gns3project` file.
3. **Boot & Verify:** Start all nodes, open your console, and begin exploring the dynamic routing tables, DHCP server leases, ACL filters, or executing end-to-end `ping` tests!
