# 🔄 Traffic Flow — End-to-End

> How a packet moves from **PC-Sales** to the **Internet (8.8.8.8)** and back,
> plus how it stays alive when **CORE-R1** dies.

## 📊 Normal Path — Sales PC → Internet

```mermaid
sequenceDiagram
    autonumber
    participant PC as 💻 Sales PC<br/>(10.10.60.10)
    participant ACC as 🟨 ACCESS-SW-SALES<br/>(VLAN 60)
    participant D1 as 🟩 DIST-SW1<br/>(HSRP Active 10.10.60.1)
    participant R1 as 🟦 CORE-R1<br/>(NAT outside Serial0/0/1)
    participant ISP as 🛰️ ISP-Router<br/>(8.8.8.1)
    participant NET as ☁️ Internet

    PC->>ACC: Ping 8.8.8.8
    ACC->>D1: Trunk to VLAN 60 (Po1)
    D1->>R1: OSPF route via Gi0/0 (10.10.255.0/30)
    R1->>R1: PAT overload → 200.100.50.5:port
    R1->>ISP: Serial0/0/1
    ISP->>NET: Static route 0.0.0.0/0 → Gi0/0
    NET-->>ISP: Reply
    ISP-->>R1: Serial
    R1-->>D1: Translate back to 10.10.60.10
    D1-->>ACC: Trunk
    ACC-->>PC: ICMP echo reply
```

## 🚨 Failover Path — CORE-R1 dies

```mermaid
sequenceDiagram
    autonumber
    participant PC as 💻 Sales PC
    participant ACC as 🟨 ACCESS-SW-SALES
    participant D1 as 🟩 DIST-SW1 (Active)
    participant D2 as 🟩 DIST-SW2 (Standby)
    participant R2 as 🟦 CORE-R2 (BGP/NAT)
    participant ISP as 🛰️ ISP-Router
    participant NET as ☁️ Internet

    Note over R1: ❌ CORE-R1 powered off
    D1->>D2: HSRP hello timeout
    D2->>D2: Promote to Active (preempt)
    PC->>ACC: Ping 8.8.8.8
    ACC->>D2: Trunk via VLAN 60
    D2->>R2: OSPF route via Gi0/1 (10.10.255.12/30)
    R2->>ISP: Serial0/0/0 (200.100.50.0/30)
    ISP->>NET: Forward
    NET-->>PC: Reply (no packet loss observed)
```

## 🛰️ Inter-VLAN Path — Finance → Sales (L3 in Distribution)

```mermaid
sequenceDiagram
    autonumber
    participant FIN as 💻 Finance PC<br/>(10.10.40.10)
    participant FINSW as 🟨 ACCESS-SW-FINANCE
    participant D1 as 🟩 DIST-SW1
    participant D1SVI as SVI VLAN 40<br/>(10.10.40.2)
    participant D1SVI60 as SVI VLAN 60<br/>(10.10.60.2)
    participant SALSW as 🟨 ACCESS-SW-SALES
    participant SAL as 💻 Sales PC<br/>(10.10.60.10)

    FIN->>FINSW: Ping 10.10.60.10
    FINSW->>D1: Trunk VLAN 40
    D1->>D1SVI: Route via HSRP gateway 10.10.40.1
    D1->>D1SVI60: Inter-VLAN route (L3)
    D1->>SALSW: Trunk VLAN 60
    SALSW->>SAL: Deliver ICMP echo
```
