<div align="center">

![Header](https://capsule-render.vercel.app/api?type=waving&color=0:1BA0D7,100:005FAD&height=200&section=header&text=Computer%20Networks%20Lab&fontSize=42&fontColor=ffffff&desc=Cisco%20Packet%20Tracer%20%E2%80%A2%20Subnetting%20%E2%80%A2%20Routing%20%E2%80%A2%20DHCP&descSize=17&descAlignY=68)

![Cisco](https://img.shields.io/badge/Cisco-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![Packet Tracer](https://img.shields.io/badge/Packet%20Tracer-9.0%2B-1BA0D7?style=for-the-badge)
![Labs](https://img.shields.io/badge/Labs-5-005FAD?style=for-the-badge)
![Routing](https://img.shields.io/badge/Routing-Static%20%7C%20RIP-0A7E8C?style=for-the-badge)
![Subnetting](https://img.shields.io/badge/Subnetting-FLSM%20%7C%20VLSM-0A7E8C?style=for-the-badge)

**Hands-on Packet Tracer labs from my Computer Networks course: LAN design, router configuration, router-based DHCP, fixed-length and variable-length subnetting, and static routing.**

[Labs](#labs-at-a-glance) • [Lab 2](#1-lab-2--lan-with-dhcp-server) • [Router Config](#2-router-configuration--two-networks) • [FLSM](#3-flsm--fixed-length-subnetting) • [VLSM](#4-vlsm--variable-length-subnetting) • [VLSM + DHCP](#5-vlsm-new--two-router-vlsm-with-dhcp) • [Getting Started](#getting-started)

</div>

---

## Labs at a Glance

| # | File | Topic | Devices | Key concepts |
|---|------|-------|---------|--------------|
| 1 | [`cn lab2.pkt`](cn%20lab2.pkt) | Large LAN with a DHCP server | 1 server, 6 switches, 110 PCs | Hierarchical switching, DHCP, server services |
| 2 | [`Router config.pkt`](Router%20config.pkt) | Router connecting two networks | 1 router, 2 switches, 6 PCs | Router interfaces, default gateways, inter-network routing |
| 3 | [`flsm.pkt`](flsm.pkt) | Fixed-length subnet masking | 1 router, 2 switches, 10 PCs | `/25` subnets, router-based DHCP pools, RIP |
| 4 | [`vlsm task.pkt`](vlsm%20task.pkt) | Variable-length subnet masking | 4 routers, 4 switches, 4 PCs | `/25` `/26` `/27` `/28` LANs, `/30` serial links, static routes |
| 5 | [`vlsm new.pkt`](vlsm%20new.pkt) | VLSM with router DHCP | 2 routers, 4 switches, 16 PCs | `/26` `/27` `/27` `/28` LANs, `/30` serial link, DHCP pools, static routes |

All files were saved with **Cisco Packet Tracer 9.0**, so open them with 9.0 or later.

---

## 1. Lab 2 — LAN with DHCP Server

**Objective:** Build a large, hierarchical LAN where one server hands out addresses to every host and provides common network services.

### Topology

```mermaid
graph TD
    S["Server0<br/>192.168.1.10"] --- C["Core Switch<br/>Switch11"]
    C --- A1["Switch6<br/>22 PCs"]
    C --- A2["Switch7<br/>22 PCs"]
    C --- A3["Switch8<br/>22 PCs"]
    C --- A4["Switch9<br/>22 PCs"]
    C --- A5["Switch9(1)<br/>22 PCs"]
```

### Design

| Item | Value |
|------|-------|
| Hosts | 110 PCs, 22 per access switch |
| Switching | 5 access switches uplinked to 1 core switch |
| Server | `Server0`, static IP `192.168.1.10` |
| DHCP pool | `serverPool`, starts at `192.168.1.11`, up to 110 users |
| Client mode | All 110 PCs use DHCP |
| Cabling | Copper straight-through throughout |

### Server services enabled

`DHCP` • `HTTP` • `FTP` • `TFTP` • `NTP` • `Syslog`

### Verify

1. Open any PC → **Desktop → IP Configuration** → select **DHCP** and confirm an address from `192.168.1.11` onward.
2. From a PC, ping `192.168.1.10` to confirm reachability to the server.
3. Open the **Web Browser** on a PC and browse to `http://192.168.1.10`.

---

## 2. Router Configuration — Two Networks

**Objective:** Configure a router with two interfaces so hosts on two different networks can communicate.

### Topology

```mermaid
graph LR
    subgraph N1["Network 192.168.1.0"]
        P0["PC2 .2"] --- SW0["Switch0"]
        P1["PC0 .3"] --- SW0
        P2["PC1 .4"] --- SW0
    end
    subgraph N2["Network 123.0.0.0"]
        P3["PC3 .2"] --- SW1["Switch1"]
        P4["PC5 .3"] --- SW1
        P5["PC4 .4"] --- SW1
    end
    SW0 ---|"Gi0/0/0<br/>192.168.1.1"| R["Router0"]
    SW1 ---|"Gi0/0/1<br/>123.168.1.1"| R
```

### Addressing

| Device | Interface | IP address | Mask | Gateway |
|--------|-----------|------------|------|---------|
| Router0 | Gi0/0/0 | 192.168.1.1 | 255.255.255.0 | n/a |
| Router0 | Gi0/0/1 | 123.168.1.1 | 255.0.0.0 | n/a |
| PC2 | NIC | 192.168.1.2 | | 192.168.1.1 |
| PC0 | NIC | 192.168.1.3 | | 192.168.1.1 |
| PC1 | NIC | 192.168.1.4 | | 192.168.1.1 |
| PC3 | NIC | 123.168.1.2 | | 123.168.1.1 |
| PC5 | NIC | 123.168.1.3 | | 123.168.1.1 |
| PC4 | NIC | 123.168.1.4 | | 123.168.1.1 |

### Key configuration

```text
Router(config)# interface GigabitEthernet0/0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown

Router(config)# interface GigabitEthernet0/0/1
Router(config-if)# ip address 123.168.1.1 255.0.0.0
Router(config-if)# no shutdown
```

### Verify

Ping from `PC0` (192.168.1.3) to `PC3` (123.168.1.2). A successful reply proves the router forwards traffic between both networks.

---

## 3. FLSM — Fixed-Length Subnetting

**Objective:** Split one `/24` network into two equal `/25` subnets, one per LAN, and let the router assign addresses with DHCP.

### Topology

```mermaid
graph LR
    subgraph L1["LAN1 · 192.168.10.0/25"]
        A["PC0 – PC4"] --- S0["Switch0"]
    end
    subgraph L2["LAN2 · 192.168.10.128/25"]
        B["PC5 – PC9"] --- S1["Switch1"]
    end
    S0 ---|"Gi0/0/0<br/>192.168.10.1"| R["Router1"]
    S1 ---|"Gi0/0/1<br/>192.168.10.129"| R
```

### Subnet plan

| Subnet | Network | Mask | Usable range | Gateway | Hosts |
|--------|---------|------|--------------|---------|-------|
| LAN1 | 192.168.10.0 | 255.255.255.128 (/25) | .1 – .126 | 192.168.10.1 | PC0 – PC4 |
| LAN2 | 192.168.10.128 | 255.255.255.128 (/25) | .129 – .254 | 192.168.10.129 | PC5 – PC9 |

Each subnet supports 126 usable hosts.

### Key configuration

```text
Router(config)# ip dhcp excluded-address 192.168.10.1
Router(config)# ip dhcp excluded-address 192.168.10.129

Router(config)# ip dhcp pool LAN1
Router(dhcp-config)# network 192.168.10.0 255.255.255.128

Router(config)# ip dhcp pool LAN2
Router(dhcp-config)# network 192.168.10.128 255.255.255.128

Router(config)# interface GigabitEthernet0/0/0
Router(config-if)# ip address 192.168.10.1 255.255.255.128
Router(config)# interface GigabitEthernet0/0/1
Router(config-if)# ip address 192.168.10.129 255.255.255.128

Router(config)# router rip
```

### Verify

1. Set every PC to **DHCP** and confirm LAN1 hosts receive `192.168.10.x` below `.128`, LAN2 hosts above it.
2. Ping from `PC0` to `PC9` to confirm routing between the two subnets.

---

## 4. VLSM — Variable-Length Subnetting

**Objective:** Divide `192.168.1.0/24` into differently sized subnets that match each LAN's host requirement, and connect four routers over `/30` serial links with static routes.

### Topology

```mermaid
graph LR
    PC0["PC0<br/>.10"] --- SW0["Switch0"] --- R1["R1<br/>LAN 192.168.1.0/25"]
    R1 ---|"192.168.1.240/30"| R2["R2<br/>LAN 192.168.1.128/26"]
    R2 ---|"192.168.1.244/30"| R3["R3<br/>LAN 192.168.1.192/27"]
    R3 ---|"192.168.1.248/30"| R4["R4<br/>LAN 192.168.1.224/28"]
    R2 --- SW1["Switch1"] --- PC1["PC1<br/>.130"]
    R3 --- SW2["Switch2"] --- PC2["PC2<br/>.194"]
    R4 --- SW3["Switch3"] --- PC3["PC3<br/>.226"]
```

### Subnet plan

| Segment | Network | Mask | Usable hosts | Router interface | Used by |
|---------|---------|------|--------------|------------------|---------|
| LAN A | 192.168.1.0 | /25 (255.255.255.128) | 126 | R1 Gi0/0/0 · 192.168.1.1 | PC0 `.10` |
| LAN B | 192.168.1.128 | /26 (255.255.255.192) | 62 | R2 Gi0/0/0 · 192.168.1.129 | PC1 `.130` |
| LAN C | 192.168.1.192 | /27 (255.255.255.224) | 30 | R3 Gi0/0/0 · 192.168.1.193 | PC2 `.194` |
| LAN D | 192.168.1.224 | /28 (255.255.255.240) | 14 | R4 Gi0/0/0 · 192.168.1.225 | PC3 `.226` |
| Link R1–R2 | 192.168.1.240 | /30 (255.255.255.252) | 2 | R1 `.241` · R2 `.242` | Serial |
| Link R2–R3 | 192.168.1.244 | /30 (255.255.255.252) | 2 | R2 `.245` · R3 `.246` | Serial |
| Link R3–R4 | 192.168.1.248 | /30 (255.255.255.252) | 2 | R3 `.249` · R4 `.250` | Serial |

Subnets are allocated largest to smallest, so there is no overlap and the address space is used efficiently.

### Static routes

| Router | Destination | Mask | Next hop |
|--------|-------------|------|----------|
| R1 | 192.168.1.128 | 255.255.255.192 | 192.168.1.242 |
| R1 | 192.168.1.192 | 255.255.255.224 | 192.168.1.242 |
| R1 | 192.168.1.224 | 255.255.255.240 | 192.168.1.242 |
| R1 | 192.168.1.244 | 255.255.255.252 | 192.168.1.242 |
| R1 | 192.168.1.248 | 255.255.255.252 | 192.168.1.242 |
| R2 | 192.168.1.0 | 255.255.255.128 | 192.168.1.241 |
| R2 | 192.168.1.192 | 255.255.255.224 | 192.168.1.246 |
| R2 | 192.168.1.224 | 255.255.255.240 | 192.168.1.246 |
| R2 | 192.168.1.248 | 255.255.255.252 | 192.168.1.246 |
| R3 | 192.168.1.0 | 255.255.255.128 | 192.168.1.245 |
| R3 | 192.168.1.128 | 255.255.255.192 | 192.168.1.245 |
| R3 | 192.168.1.224 | 255.255.255.240 | 192.168.1.250 |
| R3 | 192.168.1.240 | 255.255.255.252 | 192.168.1.245 |
| R4 | 192.168.1.0 | 255.255.255.128 | 192.168.1.249 |

### Key configuration (R1 shown)

```text
R1(config)# interface GigabitEthernet0/0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.128
R1(config-if)# no shutdown

R1(config)# interface Serial0/1/0
R1(config-if)# ip address 192.168.1.241 255.255.255.252
R1(config-if)# no shutdown

R1(config)# ip route 192.168.1.128 255.255.255.192 192.168.1.242
R1(config)# ip route 192.168.1.192 255.255.255.224 192.168.1.242
R1(config)# ip route 192.168.1.224 255.255.255.240 192.168.1.242
```

### Verify

1. Ping from `PC0` (192.168.1.10) to `PC3` (192.168.1.226). It must cross all four routers.
2. Run `show ip route` on any router to check the static entries.
3. Run `tracert 192.168.1.226` from `PC0` to see each router hop.

---

## 5. VLSM New — Two-Router VLSM with DHCP

**Objective:** Divide `192.168.100.0/24` into four LANs sized to their host counts, join two routers over a single `/30` serial link, let each router hand out addresses with DHCP, and use static routes so every LAN can reach every other LAN.

### Topology

```mermaid
graph LR
    subgraph R1G["R1"]
        direction TB
        L1["LAN1 · 192.168.100.0/26<br/>Switch0 · 8 PCs"]
        L2["LAN2 · 192.168.100.64/27<br/>Switch1 · 5 PCs"]
    end
    subgraph R2G["R2"]
        direction TB
        L3["LAN3 · 192.168.100.96/27<br/>Switch2 · 2 PCs"]
        L4["LAN4 · 192.168.100.128/28<br/>Switch3 · 1 PC"]
    end
    L1 ---|"Gi0/0/0 .1"| R1["R1"]
    L2 ---|"Gi0/0/1 .65"| R1
    R1 ===|"Serial0/1/0<br/>192.168.100.144/30<br/>.145 DCE ↔ .146"| R2["R2"]
    R2 ---|"Gi0/0/0 .97"| L3
    R2 ---|"Gi0/0/1 .129"| L4
```

### Subnet plan

| Segment | Network | Mask | Usable range | Usable hosts | Gateway / interface | PCs cabled |
|---------|---------|------|--------------|--------------|---------------------|------------|
| LAN1 | 192.168.100.0 | /26 (255.255.255.192) | .1 – .62 | 62 | R1 Gi0/0/0 · 192.168.100.1 | 8 (`PC0`–`PC7`) |
| LAN2 | 192.168.100.64 | /27 (255.255.255.224) | .65 – .94 | 30 | R1 Gi0/0/1 · 192.168.100.65 | 5 (`PC10`–`PC14`) |
| LAN3 | 192.168.100.96 | /27 (255.255.255.224) | .97 – .126 | 30 | R2 Gi0/0/0 · 192.168.100.97 | 2 (`PC15`–`PC16`) |
| LAN4 | 192.168.100.128 | /28 (255.255.255.240) | .129 – .142 | 14 | R2 Gi0/0/1 · 192.168.100.129 | 1 (`PC17`) |
| R1–R2 link | 192.168.100.144 | /30 (255.255.255.252) | .145 – .146 | 2 | R1 Serial0/1/0 `.145` (DCE) · R2 Serial0/1/0 `.146` | n/a |

Subnets are allocated from largest to smallest and sit back to back, so nothing overlaps. Addresses from `192.168.100.148` upward stay free for future growth.

### DHCP pools

Each router serves the LANs it connects to, and the gateway address is excluded from every pool.

| Router | Pool | Network | Default router | Excluded address |
|--------|------|---------|----------------|------------------|
| R1 | `LAN1` | 192.168.100.0 255.255.255.192 | 192.168.100.1 | 192.168.100.1 |
| R1 | `LAN2` | 192.168.100.64 255.255.255.224 | 192.168.100.65 | 192.168.100.65 |
| R2 | `LAN3` | 192.168.100.96 255.255.255.224 | 192.168.100.97 | 192.168.100.97 |
| R2 | `LAN4` | 192.168.100.128 255.255.255.240 | 192.168.100.129 | 192.168.100.129 |

All PCs are set to **DHCP**.

### Static routes

Each router only knows its own LANs and the serial link, so it needs a route to the LANs behind the other router.

| Router | Destination | Mask | Next hop |
|--------|-------------|------|----------|
| R1 | 192.168.100.96 | 255.255.255.224 | 192.168.100.146 |
| R1 | 192.168.100.128 | 255.255.255.240 | 192.168.100.146 |
| R2 | 192.168.100.0 | 255.255.255.192 | 192.168.100.145 |
| R2 | 192.168.100.64 | 255.255.255.224 | 192.168.100.145 |

### Key configuration (R1 shown)

```text
R1(config)# interface GigabitEthernet0/0/0
R1(config-if)# ip address 192.168.100.1 255.255.255.192
R1(config-if)# no shutdown

R1(config)# interface GigabitEthernet0/0/1
R1(config-if)# ip address 192.168.100.65 255.255.255.224
R1(config-if)# no shutdown

R1(config)# interface Serial0/1/0
R1(config-if)# ip address 192.168.100.145 255.255.255.252
R1(config-if)# clock rate 64000
R1(config-if)# no shutdown

R1(config)# ip dhcp excluded-address 192.168.100.1
R1(config)# ip dhcp excluded-address 192.168.100.65
R1(config)# ip dhcp pool LAN1
R1(dhcp-config)# network 192.168.100.0 255.255.255.192
R1(dhcp-config)# default-router 192.168.100.1
R1(config)# ip dhcp pool LAN2
R1(dhcp-config)# network 192.168.100.64 255.255.255.224
R1(dhcp-config)# default-router 192.168.100.65

R1(config)# ip route 192.168.100.96 255.255.255.224 192.168.100.146
R1(config)# ip route 192.168.100.128 255.255.255.240 192.168.100.146
```

`R2` mirrors this with `192.168.100.97/27` on Gi0/0/0, `192.168.100.129/28` on Gi0/0/1, `192.168.100.146/30` on Serial0/1/0 (no clock rate, since R1 is the DCE side), pools `LAN3` and `LAN4`, and static routes back to `192.168.100.0/26` and `192.168.100.64/27` via `192.168.100.145`.

### Verify

1. Open a PC → **Desktop → IP Configuration**, select **DHCP**, and confirm the address falls inside that LAN's range (for example `PC0` gets `192.168.100.2` or above, `PC17` gets `192.168.100.130` or above).
2. Ping from `PC0` (LAN1) to `PC17` (LAN4). The packet crosses both routers and the serial link.
3. Ping between LAN1 and LAN2 to confirm routing through R1, and between LAN3 and LAN4 to confirm routing through R2.
4. Run `show ip route` on R1 and R2 to check the connected and static (`S`) entries.
5. Run `show ip dhcp binding` on each router to see which PCs leased which address.

---

## Getting Started

1. Clone the repository:

   ```bash
   git clone https://github.com/furqanzubair209-cell/computer-networks-lab.git
   cd computer-networks-lab
   ```

2. Install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) (version 9.0 or later).
3. Open the `.pkt` file for the lab you want.
4. Use the **Verify** steps in each lab section to test connectivity.

## Repository Structure

```text
computer-networks-lab/
├── cn lab2.pkt         # LAN with DHCP server (110 PCs)
├── Router config.pkt   # Router connecting two networks
├── flsm.pkt            # Fixed-length subnetting (/25 x 2)
├── vlsm task.pkt       # Variable-length subnetting, 4 routers
├── vlsm new.pkt        # VLSM with router DHCP, 2 routers
└── README.md
```

## Skills Practiced

- IPv4 addressing and subnetting (FLSM and VLSM)
- Router interface, serial link (DCE clock rate), and static route configuration
- DHCP on both a dedicated server and on routers, with excluded gateway addresses
- Hierarchical LAN design with core and access switches
- Testing and troubleshooting with `ping`, `tracert`, `show ip route`, and `show ip dhcp binding`

## Tech Stack

![Cisco Packet Tracer](https://img.shields.io/badge/Cisco%20Packet%20Tracer-1BA0D7?style=flat-square)
![Cisco IOS](https://img.shields.io/badge/Cisco%20IOS-049fd9?style=flat-square&logo=cisco&logoColor=white)
![IPv4](https://img.shields.io/badge/IPv4-005FAD?style=flat-square)

## Author

**Muhammad Furqan**
Computer Science student, The Superior University, Lahore

[![GitHub](https://img.shields.io/badge/GitHub-furqanzubair209--cell-181717?style=flat-square&logo=github)](https://github.com/furqanzubair209-cell)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-muhammad--furqan-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/muhammad-furqan-228807304)

<div align="center">

![Footer](https://capsule-render.vercel.app/api?type=waving&color=0:005FAD,100:1BA0D7&height=100&section=footer)

</div>
