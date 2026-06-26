# 🧩 Protocol Stack

> Every protocol that runs in the network and on which layer of the topology.

```mermaid
flowchart TB
    subgraph L7["📚 Layer 7 — Application / Management Plane"]
      SSH["SSH v2 (rsa 1024)"]
      SNMP["SNMP (ready)"]
      SYSLOG["Syslog (ready)"]
    end

    subgraph L4["🛡️ Layer 4 — Security & Translation"]
      NAT["NAT/PAT overload"]
      ACL["ACL 1 — permit 10.10.0.0/16"]
      PORT["Port Security + BPDU Guard"]
    end

    subgraph L3["🌐 Layer 3 — Routing"]
      OSPF["OSPF Area 0"]
      BGP["BGP (simulated)"]
      HSRP["HSRP v1"]
      STATIC["Static (ISP ↔ Core)"]
    end

    subgraph L2["🔌 Layer 2 — Switching"]
      VLAN["VLANs 10/20/30/40/50/60/99/100/150"]
      DOT1Q["802.1Q Trunk"]
      STP["PVST+ · PortFast · BPDU Guard"]
      EC["EtherChannel Po1 (active)"]
    end

    L7 --> L4 --> L3 --> L2

    classDef l7 fill:#0d9488,stroke:#0d9488,color:#fff;
    classDef l4 fill:#9333ea,stroke:#9333ea,color:#fff;
    classDef l3 fill:#2563eb,stroke:#2563eb,color:#fff;
    classDef l2 fill:#65a30d,stroke:#65a30d,color:#fff;

    class SSH,SNMP,SYSLOG l7;
    class NAT,ACL,PORT l4;
    class OSPF,BGP,HSRP,STATIC l3;
    class VLAN,DOT1Q,STP,EC l2;
```

## 🗃️ Where each protocol lives

| Protocol | Where it runs | Purpose |
|----------|---------------|---------|
| OSPF | CORE-R1, CORE-R2, DIST-SW1, DIST-SW2 | Dynamic internal routing |
| BGP | CORE-R1, CORE-R2 ↔ ISP | Simulated external routing |
| HSRP | DIST-SW1, DIST-SW2 | First-hop gateway redundancy |
| NAT/PAT | CORE-R1, CORE-R2 | Internet address sharing |
| 802.1Q | All trunks | VLAN tagging |
| PVST+ | All switches | Per-VLAN spanning tree |
| PortFast | Access ports | Fast edge transition |
| BPDU Guard | Access ports | Stops rogue switches |
| EtherChannel | Access ↔ Distribution | Aggregated GigE uplinks |
| SSH v2 | All managed devices | Encrypted management |
