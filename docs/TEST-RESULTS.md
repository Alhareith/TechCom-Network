# ✅ Test Results

> All tests were executed in **Cisco Packet Tracer 8.x** on the live
> `Network Ayman & Alhareth.pkt` lab file. Every test reported **100% success**.

## 🧪 Test Matrix

| # | Test                                  | Tool / Method              | Expected                                  | Result |
|---|---------------------------------------|----------------------------|-------------------------------------------|--------|
| 1 | Inter-department L3 connectivity      | `ping` between PCs         | All replies, < 5 ms                       | ✅ Pass |
| 2 | Internet reachability after NAT       | `ping 8.8.8.1` from PC     | Reply via PAT overload on `Serial0/0/x`   | ✅ Pass |
| 3 | Path visibility across the core       | `traceroute 8.8.8.1`       | DIST-SW → CORE-R → ISP                    | ✅ Pass |
| 4 | HSRP failover                         | Power off CORE-R1          | DIST-SW2 takes over with no packet loss   | ✅ Pass |
| 5 | BPDU Guard / unauthorized device     | Plug rogue switch          | Access port err-disabled within 1 s       | ✅ Pass |
| 6 | OSPF neighbor adjacency               | `show ip ospf neighbor`    | Full adjacency on both core routers       | ✅ Pass |

## 🔬 Detailed Walk-through

### Test 1 — Ping across VLANs (Finance → Sales)
```
C:\> ping 10.10.60.10

Pinging 10.10.60.10 with 32 bytes of data:
Reply from 10.10.60.10: bytes=32 time=2ms TTL=126
Reply from 10.10.60.10: bytes=32 time=1ms TTL=126
Reply from 10.10.60.10: bytes=32 time=2ms TTL=126
Reply from 10.10.60.10: bytes=32 time=2ms TTL=126

Ping statistics for 10.10.60.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

### Test 4 — HSRP Failover
1. `show standby brief` on DIST-SW1 → state **Active** for all VLANs.
2. Power off CORE-R1 in Packet Tracer.
3. Wait ~3 s (HSRP hold-down).
4. `show standby brief` on DIST-SW2 → state **Active** for all VLANs.
5. Continuous `ping 10.10.60.10` from a Sales PC → **0 packets lost**.

### Test 5 — BPDU Guard
1. Connect a rogue unmanaged switch to `Fa0/5` of ACCESS-SW-FINANCE.
2. Within **1 second**, the port transitions to `err-disabled`.
3. Console log:
   ```
   %SPANTREE-2-BLOCK_BPDUGUARD: Received BPDU on port Fa0/5 with BPDU Guard enabled. Disabling port.
   %PM-4-ERR_DISABLE: bpduguard error detected on Fa0/5
   ```
