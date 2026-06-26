# 🌐 Architecture Overview

> High-level topology of the **TechCom** enterprise network — three-tier
> (Core / Distribution / Access) with redundant uplinks, HSRP, OSPF, and
> dual-homed ISP connectivity.

## 📐 Three-Tier Diagram (Mermaid)

```mermaid
flowchart TB
    Internet(["☁️ Internet<br/>(ISP Cloud)"])
    ISP["🛰️ ISP-Router<br/>8.8.8.1/24"]
    R1["🟦 CORE-R1<br/>(HSRP Active · NAT · BGP)"]
    R2["🟦 CORE-R2<br/>(HSRP Standby · NAT · BGP)"]
    D1["🟩 DIST-SW1<br/>(L3 · HSRP Active)"]
    D2["🟩 DIST-SW2<br/>(L3 · HSRP Standby)"]
    subgraph ACCESS["🟨 Access Layer — One switch per department"]
        SW_IT["ACCESS-SW-IT<br/>VLAN 20 + Voice 150"]
        SW_SUP["ACCESS-SW-SUPPORT<br/>VLAN 30"]
        SW_FIN["ACCESS-SW-FINANCE<br/>VLAN 40"]
        SW_HR["ACCESS-SW-HR<br/>VLAN 50"]
        SW_SAL["ACCESS-SW-SALES<br/>VLAN 60"]
        SW_MGT["ACCESS-SW-MGMT<br/>VLAN 10 only"]
    end

    Internet --- ISP
    ISP ---|Se0/0/0 200.100.50.0/30| R2
    ISP ---|Se0/0/1 200.100.50.4/30| R1
    R1 ---|Gi0/0 10.10.255.0/30| D1
    R1 ---|Gi0/1 10.10.255.8/30| D2
    R2 ---|Gi0/0 10.10.255.4/30| D1
    R2 ---|Gi0/1 10.10.255.12/30| D2

    D1 ---|Trunk + EtherChannel Po1| SW_IT
    D2 ---|Trunk + EtherChannel Po1| SW_IT
    D1 --- SW_SUP
    D2 --- SW_SUP
    D1 --- SW_FIN
    D2 --- SW_FIN
    D1 --- SW_HR
    D2 --- SW_HR
    D1 --- SW_SAL
    D2 --- SW_SAL
    D1 ---|Trunk VLAN 10 only| SW_MGT
    D2 ---|Trunk VLAN 10 only| SW_MGT

    classDef core fill:#1f3a8a,stroke:#1e3a8a,color:#fff,stroke-width:2px;
    classDef dist fill:#0e7490,stroke:#0e7490,color:#fff,stroke-width:2px;
    classDef acc  fill:#a16207,stroke:#a16207,color:#fff,stroke-width:2px;
    classDef isp  fill:#374151,stroke:#374151,color:#fff,stroke-width:2px;
    classDef net  fill:#16a34a,stroke:#16a34a,color:#fff,stroke-width:2px;
    class R1,R2 core;
    class D1,D2 dist;
    class SW_IT,SW_SUP,SW_FIN,SW_HR,SW_SAL,SW_MGT acc;
    class ISP isp;
    class Internet net;
```

## 🧱 Layer Responsibilities

| Layer | Devices | Responsibility |
|---|---|---|
| 🌐 **Internet / ISP** | ISP-Router | Static-route traffic sink + dual serial uplinks |
| 🟦 **Core** | CORE-R1, CORE-R2 | NAT/PAT, BGP with ISP, OSPF area 0, HSRP with distribution |
| 🟩 **Distribution** | DIST-SW1, DIST-SW2 | Inter-VLAN routing (L3 SVIs), HSRP virtual gateways, OSPF peering |
| 🟨 **Access** | 6 × ACCESS-SW-* | Department segmentation, port security, BPDU Guard, VoIP tagging |

## 🔁 Redundancy Strategy

* **Two core routers** — every distribution switch has a link to **both**
  cores. Loss of either core is invisible to end users (HSRP failover).
* **Two distribution switches** — every access switch uplinks to **both**
  distribution switches via separate trunks + an EtherChannel on the
  GigE ports.
* **Two serial uplinks to ISP** — equal-cost static routes from ISP into
  the customer space mean no single-link outage.
