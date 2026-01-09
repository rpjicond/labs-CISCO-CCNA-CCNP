# Inter-AS Routing: BGP, OSPF, EIGRP & Route Reflector

## 🎯 Objective
This lab demonstrates advanced routing integration between three different Autonomous Systems (AS), focusing on BGP scalability and redistribution between IGPs (OSPF and EIGRP).

## 🗺️ Topology
![Network Topology](topology.png)

## 🏗️ Architecture
*   **AS 65000 (Left/Orange):** Internal routing using **OSPF**. Focus on eBGP peering at the edge.
*   **AS 65001 (Center/Pink):** Core Service Provider network using **OSPF** as IGP. It implements **iBGP with a Route Reflector (RR)** on Router IP3 to solve the BGP Full Mesh requirement.
*   **AS 65002 (Right/Green):** Internal routing using **EIGRP**.

## 🛠️ Technologies & Features
- **eBGP & iBGP**: Inter-AS connectivity and internal route propagation.
- **BGP Route Reflector**: Configured on IP3 to reflect routes to neighbors (IP1, IP2, IP4, IP5).
- **Route Redistribution**: Mutual redistribution between BGP and IGPs to ensure end-to-end reachability.
- **Loopback Peering**: Best practice for BGP session stability.

## ✅ Verification & Results
- **Control Plane**: `show ip bgp summary` and `show ip bgp` confirm all prefixes are learned.
- **OSPF Adjacencies**: Confirmed in FULL state within the core.
- **Data Plane**: Successful `traceroute` from AS 65000 (C1) to AS 65002 (C4), proving correct path selection across multiple AS.

<img width="1615" height="858" alt="Captura de ecrã 2026-01-09 031111" src="https://github.com/user-attachments/assets/e720fb45-4827-4219-b83f-3a8c1a88b54b" />
<img width="1591" height="842" alt="Captura de ecrã 2026-01-09 031205" src="https://github.com/user-attachments/assets/4bec97dc-47e8-45eb-9270-595752627f3a" />
<img width="1599" height="825" alt="Captura de ecrã 2026-01-09 031329" src="https://github.com/user-attachments/assets/2fcf4c62-5a81-4e62-8e59-ab9cd37355fb" />
<img width="1596" height="852" alt="Captura de ecrã 2026-01-09 031349" src="https://github.com/user-attachments/assets/146c22ed-95df-4ee4-ab35-04514d12c24a" />
<img width="1653" height="819" alt="Captura de ecrã 2026-01-09 031432" src="https://github.com/user-attachments/assets/5f74831c-8b3f-42f8-a443-8bf6fd2ef283" />
<img width="1342" height="804" alt="Captura de ecrã 2026-01-09 031539" src="https://github.com/user-attachments/assets/4358666f-05f8-4d7e-a26e-8d02f9fb95e2" />

## 🚀 Key Configurations
Example of Route Reflector configuration on **IP3**:
```cisco
router bgp 65001
 neighbor 8.1.3.2 remote-as 65001
 neighbor 8.1.3.2 route-reflector-client
 neighbor 8.1.4.2 remote-as 65001
 neighbor 8.1.4.2 route-reflector-client
