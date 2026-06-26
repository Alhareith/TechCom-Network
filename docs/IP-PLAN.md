# 📐 IP Addressing Plan

> Full IPv4 plan for the **TechCom** network. Every subnet is sized to a single
> department / function, with a single `/30` link subnet for every point-to-point
> core-to-distribution or core-to-ISP uplink.

## 🌐 Address Space

| Block | Purpose |
|---|---|
| `10.10.0.0/16` | Entire customer space (internal) |
| `200.100.50.0/24` | Public / ISP edge (split into `/30` links) |
| `8.8.8.0/24` | Simulated Internet on the ISP router |

## 🛰️ Core ↔ Distribution Point-to-Point Links

| Link | Network | Endpoint A | Endpoint B |
|---|---|---|---|
| DIST-SW1 ↔ CORE-R1 | `10.10.255.0/30` | `10.10.255.1` | `10.10.255.2` |
| DIST-SW1 ↔ CORE-R2 | `10.10.255.4/30` | `10.10.255.5` | `10.10.255.6` |
| DIST-SW2 ↔ CORE-R1 | `10.10.255.8/30` | `10.10.255.9` | `10.10.255.10` |
| DIST-SW2 ↔ CORE-R2 | `10.10.255.12/30` | `10.10.255.13` | `10.10.255.14` |

## 🌐 Core ↔ ISP Links

| Link | Network | Endpoint Core | Endpoint ISP |
|---|---|---|---|
| CORE-R1 ↔ ISP | `200.100.50.4/30` | `200.100.50.5` | `200.100.50.6` |
| CORE-R2 ↔ ISP | `200.100.50.0/30` | `200.100.50.1` | `200.100.50.2` |
| ISP → Internet | `8.8.8.0/24` | — | `8.8.8.1` |

## 🏢 Department / Function Subnets

| VLAN | Name | Subnet | HSRP GW | DIST-SW1 SVI | DIST-SW2 SVI | Access SVI |
|---|---|---|---|---|---|---|
| 10  | Management | `10.10.10.0/24`   | `10.10.10.1`  | `.2` (Active) | `.3` (Standby) | `.254` |
| 20  | IT          | `10.10.20.0/24`   | `10.10.20.1`  | `.2` (Active) | `.3` (Standby) | `.254` |
| 30  | Support     | `10.10.30.0/24`   | `10.10.30.1`  | `.2` (Active) | `.3` (Standby) | `.254` |
| 40  | Finance     | `10.10.40.0/24`   | `10.10.40.1`  | `.2` (Active) | `.3` (Standby) | `.254` |
| 50  | HR          | `10.10.50.0/24`   | `10.10.50.1`  | `.2` (Active) | `.3` (Standby) | `.254` |
| 60  | Sales       | `10.10.60.0/24`   | `10.10.60.1`  | `.2` (Active) | `.3` (Standby) | `.254` |
| 99  | Mgmt-Devices| `10.10.99.0/24`   | `10.10.99.1`  | `.2` (Active, prio 110) | `.3` (Standby, prio 90) | — |
| 100 | Native      | `10.10.100.0/24`  | — | trunk native | trunk native | trunk native |
| 150 | Voice (VoIP)| `10.10.150.0/24`  | `10.10.150.1` | `.2` (Active) | `.3` (Standby) | voice on access ports |

> **Naming note:** VLAN 10 (`Management`) and VLAN 99 (`Mgmt`) intentionally
> coexist — VLAN 10 carries the management SVIs while VLAN 99 carries the
> device-plane traffic between routers and switches. They are kept logically
> separate for ACL granularity.
