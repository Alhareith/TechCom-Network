# 🧠 Challenges & Solutions

> Every bump we hit while designing and verifying the network — and exactly how
> we engineered around it.

## 1. 🚧 Duplicate IPs between distribution switches (VLAN 30)

**Problem:** Both DIST-SW1 and DIST-SW2 wanted to own `10.10.30.2`,
collapsing the SVI.

**Fix:** Split the addresses (`DIST-SW1=.2`, `DIST-SW2=.3`) and let **HSRP**
present a single virtual gateway (`10.10.30.1`) to end hosts. DHCP scope was
re-aligned to that virtual IP.

## 2. 🚧 HSRP Active / Standby out of sync

**Problem:** Without priority tuning both cores could try to be Active and
flip-flop.

**Fix:** Hard-set `standby X priority 110` on DIST-SW1 and add `preempt` on
both sides so the higher-priority switch always wins back after recovery.
Verified by powering off CORE-R1 mid-call.

## 3. 🚧 Native VLAN mismatch on trunks

**Problem:** Mixing VLAN 1 and VLAN 100 as native caused 802.1Q tagging
glitches and rogue broadcasts across the fabric.

**Fix:** Standardised on **VLAN 100** as native everywhere, validated with
`show interface trunk` on every link.

## 4. 🚧 Inter-VLAN routing silent failure

**Problem:** Forgetting `ip routing` or the per-VLAN SVI silently disabled
all cross-VLAN traffic.

**Fix:** Enabled `ip routing`, built an SVI for every VLAN, and validated
end-to-end with `ping` from each department.

## 5. 🚧 NAT on the wrong interface

**Problem:** Tagging the inside/outside backwards broke Internet access for
every department.

**Fix:** Explicit `ip nat inside` on `Gi0/0`/`Gi0/1` and `ip nat outside` on
the serial link; ACL `1 permit 10.10.0.0 0.0.255.255` on the overload rule.

## 6. 🚧 VoIP one-way / no dial-tone

**Problem:** Phones wouldn't register because the Voice VLAN wasn't
reachable.

**Fix:** Unify VLAN 150 across every switch, add HSRP for the voice gateway,
and tag phones via `switchport voice vlan 150` on every access port.

## 7. 🚧 Single Point of Failure on the distribution layer

**Problem:** Originally every access switch had only one uplink.

**Fix:** Added a **second distribution switch (DIST-SW2)** with parallel
uplinks, enabled EtherChannel `Po1` on the Gigabit uplinks, and validated
failover by yanking one uplink mid-stream — no drops.

---

> 💬 **Personal note from the team**
>
> *"Designing this network taught us that networking is not just plugging in
> cables — it's the art of engineering availability and stability. Every fix
> here was tested live in Packet Tracer to guarantee the highest level of
> performance and reliability."*
> — **Alhareth Hayel & Ayman Bashi Al-Baidhani**
