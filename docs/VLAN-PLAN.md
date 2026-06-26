# 🗂️ VLAN & Service Plan

> Every VLAN in the network, what it carries, and where it lives on the wire.

## 🧾 VLAN Table

| VLAN ID | Name              | Role / Description                                       | Status   | Notes |
|--------:|-------------------|----------------------------------------------------------|----------|-------|
| **10**  | Management        | Network-management SVI + management-plane traffic        | Active   | Where routers talk to switches |
| **20**  | IT                | IT department user VLAN                                  | Active   | Voice VLAN 150 piggy-backed |
| **30**  | Support           | Technical Support department                             | Active   | `ip helper-address` for DHCP |
| **40**  | Finance           | Finance department                                       | Active   | ACL target for sensitive traffic |
| **50**  | HR                | Human Resources                                          | Active   | Isolated from voice R&D |
| **60**  | Sales             | Sales department                                         | Active   | QoS ready for CRM traffic |
| **99**  | Mgmt-Devices      | Router / switch device-plane traffic                     | Active   | Lower HSRP prio on DIST-SW2 (90) |
| **100** | Native            | Trunk native VLAN — must match on every trunk           | Active   | Unified across the whole fabric |
| **150** | Voice (VoIP)      | IP Phones — separate broadcast domain                   | Active   | HSRP Active on DIST-SW1 |

## 🎯 VLAN 10 vs VLAN 99 — why both?

* **VLAN 10** is the SVI / "office" management VLAN — where a network
  administrator sits at his workstation and runs SSH, monitoring, and config tools.
* **VLAN 99** is the *device-plane* VLAN — the channel routers and switches use
  to talk to each other (routing protocols, HSRP hellos, syslog, SNMP).

Keeping them separate means **no user device can ever speak management traffic**,
shrinks the attack surface, and lets us drop strict ACLs on the boundary.

## 🔐 Trunk Discipline

* All access-to-distribution trunks use **dot1Q** with **VLAN 100** as native.
* Trunks **explicitly enumerate** the allowed VLANs (`switchport trunk allowed
  vlan ...`) — VLAN hopping is blocked by configuration, not by hope.
* **MANAGEMENT** trunks carry **only** VLAN 10 — pure separation.

## 🎙️ Voice VLAN 150

* Tagged as `switchport voice vlan 150` on every access port that may connect
  an IP phone.
* Routed via HSRP `10.10.150.1` with DIST-SW1 active.
* Designed for future QoS policy (`mls qos`, `priority-queue out`).
