# ⚖️ HSRP Failover — State Machine

> How the gateway role migrates between DIST-SW1 (Active) and DIST-SW2
> (Standby) when a core router dies.

```mermaid
stateDiagram-v2
    [*] --> Normal: Power-on
    Normal: 🟢 Normal Operation
    Normal: DIST-SW1 = Active (prio 110)\nDIST-SW2 = Standby (prio 100)\nGateway = virtual HSRP IP

    Normal --> Detected: CORE-R1 dies\nHSRP hello missed ×3
    Detected: 🟡 Failover In Progress
    Detected: DIST-SW2 detects timeout\n→ claim Active (preempt)

    Detected --> Switched: HSRP gratuitous ARP sent
    Switched: 🟠 Switched Operation
    Switched: DIST-SW2 = Active\nDIST-SW1 = Standby\nTraffic now flows via CORE-R2

    Switched --> Recovered: CORE-R1 back online
    Recovered: 🟢 Back to Normal
    Recovered: DIST-SW1 reclaims Active\n(preempt + higher priority)
    Recovered --> Normal
```

## 🧮 HSRP Priority Table

| VLAN | DIST-SW1 | DIST-SW2 | Active |
|------|---------:|---------:|--------|
| 10   | **110**  | 100      | DIST-SW1 |
| 20   | **110**  | 100      | DIST-SW1 |
| 30   | **110**  | 100      | DIST-SW1 |
| 40   | **110**  | 100      | DIST-SW1 |
| 50   | **110**  | 100      | DIST-SW1 |
| 60   | **110**  | 100      | DIST-SW1 |
| 99   | **110**  | 90       | DIST-SW1 |
| 150  | **110**  | 100      | DIST-SW1 |

> VLAN 99 intentionally has DIST-SW2 at **priority 90** — lowest of the
> bunch — so the device-plane gateway never flaps during routine
> reconvergence.
