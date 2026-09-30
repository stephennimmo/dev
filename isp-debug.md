# Internet Troubleshooting: My Side vs. the ISP

Runbook for isolating home internet outages. Work from the inside out and stop at the first layer that fails.

## Topology

```
Laptop / Wi-Fi clients
        │
   Deco X90 (lan2, VLAN 2 untagged)
        │
   MikroTik RouterOS 7  ── bond-switch (VLANs 3-4 tagged) ── lab switch
        │
      wan (DHCP client)
        │
   DZS 5302 XGS-PON ONT (bridge only, ISP-managed, no UI)
        │
      Fiber → ISP
```

## 1. Laptop → Router

```bash
ping 10.1.0.1
```

Fails → Wi-Fi or Deco problem. Test wired, check the Deco.

> TTL=63 is expected while the Deco is in router mode (one extra routed hop).

## 2. Laptop → Internet (by IP)

```bash
ping 1.1.1.1
```

Step 1 passes but this fails → move to the router.

## 3. Name Resolution

```bash
dig google.com
```

IPs work but names don't → DNS. Check the MikroTik resolver:

```
/ip dns print
```

This is on my side.

## 4. Router → Internet

```
/ping 1.1.1.1 count=3
```

| Router | Laptop | Meaning |
|--------|--------|---------|
| OK | Fails | Forward chain, NAT, or Deco — my side |
| Fails | Fails | Continue to step 5 |

> This test is only valid because the raw ICMP rule drops echo *requests* only (see [Firewall Notes](#firewall-notes)).

## 5. WAN Link, Lease, and Route

```
/interface print where name=wan
/ip dhcp-client print detail
/ip route print where dst-address=0.0.0.0/0
```

- Link down or lease not `bound` → ONT, cable, or ISP.
- Default route missing or not flagged `A` (active) → my config.
- `expires-after` near the full lease time plus an NTP clock jump in `/log print` → the WAN link just bounced.

## 6. Layer 2 to the ISP Gateway

```
/ip arp print where address=<gateway-ip>
```

ARP incomplete → nothing answering on the WAN segment. Power-cycle the ONT (off ~30s), then:

```
/ip dhcp-client release [find]
```

Check the ONT LEDs: solid PON = registered with the ISP; LOS/Alarm red = fiber problem.

## 7. The Deciding Test: Packet Capture

Run the sniffer, then ping from a second terminal:

```
/tool sniffer quick interface=wan ip-protocol=icmp
/ping 1.1.1.1 count=5
```

| Capture shows | Meaning |
|---------------|---------|
| Requests out, **no replies** | ISP problem — call them |
| Replies arrive, ping still fails | My firewall is dropping them |

If it's the firewall, reset counters, ping, and see which drop rule climbs:

```
/ip firewall raw reset-counters-all
/ip firewall filter reset-counters-all
/ping 1.1.1.1 count=5
/ip firewall raw print stats
/ip firewall filter print stats
```

## 8. Where Does It Die?

```
/tool traceroute 1.1.1.1
```

- Dies at hop 1–2 → ISP edge.
- Dies further out → upstream/peering. Still not my side.

## Calling the ISP

Have this ready:

- ONT power-cycled
- Lease is `bound` (IP and gateway from `/ip dhcp-client print detail`)
- ARP to the gateway resolves
- Packet capture shows requests leaving the WAN with no replies
- ONT serial number and MAC (label on the bottom of the DZS 5302)

## Firewall Notes

Raw `prerouting` runs **before** connection tracking, so it can't tell unsolicited traffic from replies. A blanket `protocol=icmp action=drop` there breaks:

- The router's own pings and traceroutes
- Ping/traceroute from LAN clients
- Path MTU Discovery (ICMP type 3 code 4, "fragmentation needed")

Drop only inbound echo requests:

```
/ip firewall raw add chain=prerouting in-interface=wan protocol=icmp icmp-options=8:0-255 action=drop comment="Drop inbound ICMP echo requests from WAN"
```

The filter `input` chain handles the rest (accept established/related, drop other new input from WAN).

## Habits

- After any Ansible run against the router, run `/ping 1.1.1.1` so config regressions show up immediately instead of looking like an outage.
- Clear the RouterOS terminal with **Ctrl+L** (there's no `clear` command).
