# 📚 Documentation Index

> Quick jump table for every document in this repository.

| 📄 File | Purpose | Read time |
|---|---|---|
| 🏠 [../README.md](../README.md) | Project home / overview | 8 min |
| 📐 [IP-PLAN.md](IP-PLAN.md) | Every subnet, every gateway | 4 min |
| 🗂️ [VLAN-PLAN.md](VLAN-PLAN.md) | VLAN table + trunk discipline | 3 min |
| ✅ [TEST-RESULTS.md](TEST-RESULTS.md) | All tests with sample output | 4 min |
| 🧠 [CHALLENGES.md](CHALLENGES.md) | Engineering war stories | 4 min |
| 🚀 [GITHUB-UPLOAD-PLAN.md](GITHUB-UPLOAD-PLAN.md) | Step-by-step upload guide | 6 min |
| 🌐 [../diagrams/01-architecture-overview.md](../diagrams/01-architecture-overview.md) | 3-tier Mermaid diagram | 2 min |
| 🔄 [../diagrams/02-traffic-flow.md](../diagrams/02-traffic-flow.md) | End-to-end sequence diagrams | 4 min |
| 🗺️ [../diagrams/03-vlan-broadcast-domains.md](../diagrams/03-vlan-broadcast-domains.md) | VLAN broadcast domains | 2 min |
| ⚖️ [../diagrams/04-hsrp-failover-state.md](../diagrams/04-hsrp-failover-state.md) | HSRP state machine | 2 min |
| 🧩 [../diagrams/05-protocol-stack.md](../diagrams/05-protocol-stack.md) | L2-L7 protocol stack | 2 min |
| 📑 [arabic/telecom_network_project.pdf](arabic/telecom_network_project.pdf) | Original Arabic report (32 pages) | 25 min |
| 🎨 [network_topology.pptx](network_topology.pptx) | Presentation slides (PowerPoint) | — |

---

## 🗺️ Reading Order for First-Time Visitors

1. **Start** with [README.md](../README.md) — get the 30,000-foot view.
2. **Look** at [01-architecture-overview.md](../diagrams/01-architecture-overview.md) — see the topology.
3. **Walk through** [02-traffic-flow.md](../diagrams/02-traffic-flow.md) — understand how packets flow.
4. **Drill** into [IP-PLAN.md](IP-PLAN.md) + [VLAN-PLAN.md](VLAN-PLAN.md).
5. **Read** [CHALLENGES.md](CHALLENGES.md) for context.
6. **Verify** with [TEST-RESULTS.md](TEST-RESULTS.md).
7. **Open** the lab in Packet Tracer — `packet-tracer/Network_Ayman_Alhareth.pkt`.

---

## 🧰 For Network Engineers (skip-the-fluff path)

If you only have 5 minutes:

```bash
configs/configs-Router/CORE-R1.conf      ← OSPF + HSRP + NAT
configs/configs-Router/CORE-R2.conf      ← Same, standby
configs/configs-Switch/DIST-SW1.conf     ← HSRP Active + L3 SVIs
configs/configs-Switch/DIST-SW2.conf     ← HSRP Standby
configs/configs-Switch/ACCESS-SW-*.conf  ← Per-department access
configs/configs-ISP/ISP-Router.conf      ← External gateway
```

That gives you the entire production config in ~20 KB.
