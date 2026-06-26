# 🗺️ VLAN Broadcast Domains

> A picture of every broadcast domain in the network, who owns it, and how
> it crosses the trunk fabric.

## 🎨 Domain Map

```mermaid
flowchart LR
    subgraph VLAN10["🟦 VLAN 10 — Management (10.10.10.0/24)"]
      M10[MGMT Hosts<br/>SSH / SNMP / Syslog]
    end
    subgraph VLAN20["🟩 VLAN 20 — IT (10.10.20.0/24)"]
      M20[IT Users + IP Phones]
    end
    subgraph VLAN30["🟨 VLAN 30 — Support (10.10.30.0/24)"]
      M30[Support Agents]
    end
    subgraph VLAN40["🟥 VLAN 40 — Finance (10.10.40.0/24)"]
      M40[Finance Staff + ACL target]
    end
    subgraph VLAN50["🟪 VLAN 50 — HR (10.10.50.0/24)"]
      M50[HR Staff]
    end
    subgraph VLAN60["🟧 VLAN 60 — Sales (10.10.60.0/24)"]
      M60[Sales Team + QoS]
    end
    subgraph VLAN99["⬛ VLAN 99 — Device-plane (10.10.99.0/24)"]
      M99[Routers ↔ Switches<br/>OSPF · HSRP hellos]
    end
    subgraph VLAN100["⬜ VLAN 100 — Native (trunk only)"]
      M100[802.1Q native · no data]
    end
    subgraph VLAN150["🎙️ VLAN 150 — Voice / VoIP (10.10.150.0/24)"]
      M150[IP Phones<br/>HSRP 10.10.150.1]
    end

    M10 --- TRUNK1[Trunk dot1Q<br/>allowed: 10]
    M20 --- TRUNK2[Trunk dot1Q<br/>allowed: 20,150]
    M30 --- TRUNK3[Trunk dot1Q<br/>allowed: 30,150]
    M40 --- TRUNK4[Trunk dot1Q<br/>allowed: 40,150]
    M50 --- TRUNK5[Trunk dot1Q<br/>allowed: 50,150]
    M60 --- TRUNK6[Trunk dot1Q<br/>allowed: 60,150]
    M99 --- TRUNK7[Trunk dot1Q<br/>allowed: 10,20,30,40,50,60,99,150]
    M150 --- TRUNK2
    M150 --- TRUNK3
    M150 --- TRUNK4
    M150 --- TRUNK5
    M150 --- TRUNK6

    classDef mgmt fill:#1e3a8a,stroke:#1e3a8a,color:#fff;
    classDef it fill:#15803d,stroke:#15803d,color:#fff;
    classDef sup fill:#a16207,stroke:#a16207,color:#fff;
    classDef fin fill:#b91c1c,stroke:#b91c1c,color:#fff;
    classDef hr  fill:#7e22ce,stroke:#7e22ce,color:#fff;
    classDef sal fill:#c2410c,stroke:#c2410c,color:#fff;
    classDef dp  fill:#111827,stroke:#111827,color:#fff;
    classDef nat fill:#525252,stroke:#525252,color:#fff;
    classDef voi fill:#0d9488,stroke:#0d9488,color:#fff;

    class M10 mgmt;
    class M20 it;
    class M30 sup;
    class M40 fin;
    class M50 hr;
    class M60 sal;
    class M99 dp;
    class M100 nat;
    class M150 voi;
```

## 🔒 Trunk Allow-Lists (defence-in-depth)

| Trunk | Native | Allowed VLANs |
|---|---|---|
| DIST ↔ Access (data) | 100 | `20,30,40,50,60,150` |
| DIST ↔ Access (mgmt) | 100 | `10` *(only)* |
| DIST internal / to core | 100 | `10,20,30,40,50,60,99,150` |

> 🚫 **VLAN hopping blocked**: every trunk enumerates exactly which VLANs are
> permitted, instead of the dangerous default of *all*.
