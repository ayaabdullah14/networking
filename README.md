# ENCS3320 – Computer Networks: Project 2
**Cisco Packet Tracer Network Simulation**
Birzeit University · Faculty of Engineering & Technology · Electrical & Computer Engineering

---

## Team

| Name | ID | Section |
|---|---|---|
| Aya Abdullah | 1220782 | 1 |
| Lana Darmna | 1220588 | 1 |
| Jeneen Sattof | 1221682 | 1 |

**Instructor:** Alhareth Zyoud
**Date:** 18 June 2025

---

## Project Overview

A full network simulation built in Cisco Packet Tracer covering subnetting, multi-area OSPF routing, and four essential network services: DHCP, Web, Email (SMTP/POP3), and DNS. The base address block is **105.88.8.0/23** (derived from student ID 1220582, X=5, Y=88).

---

## Network Areas

| Area | Name | Subnet | Hosts |
|---|---|---|---|
| 0 | Core (backbone) | NET0-A/B/C – three /30 links | 2 each |
| 1 | University | NET1-A `105.88.8.64/26` · NET1-B `105.88.8.128/26` | 60 each |
| 2 | Street | NET2 `105.88.8.192/27` | 30 |
| 3 | Home | NET3 `105.88.8.224/27` | 20 |
| 4 | Datacenter | NET4 `105.88.9.0/28` | 15 |

### Area descriptions

- **Core (Area 0):** Three routers in a triangle topology connected via serial /30 links (NET0-A, NET0-B, NET0-C).
- **University (Area 1):** Wired subnet (NET1-A) with two PCs + DHCP server; wireless subnet (NET1-B) with access point `ENCS3320T003` (WPA2) serving a laptop, smartphone, and tablet.
- **Street (Area 2):** Central-Office server + Cell Tower broadcasting 3G/4G to three smartphones via DHCP.
- **Home (Area 3):** Two PCs (PC2, PC3) with static IPs connected through a single switch.
- **Datacenter (Area 4):** Three servers — `www.coe.birzeit.edu` (Web), `mail.coe.birzeit.edu` (Email), `dns.coe.birzeit.edu` (DNS) — all with static IPs.

---

## IP Address Summary

| Device / Network | IP Address | Mask |
|---|---|---|
| NET0-A | 105.88.8.0 | /30 |
| NET0-B | 105.88.8.4 | /30 |
| NET0-C | 105.88.8.8 | /30 |
| NET1-A gateway | 105.88.8.65 | /26 |
| NET1-B gateway | 105.88.8.129 | /26 |
| NET2 gateway | 105.88.8.193 | /27 |
| NET3 gateway | 105.88.8.225 | /27 |
| NET4 gateway | 105.88.9.1 | /28 |
| Web server | 105.88.9.4 | /28 |
| Mail server | 105.88.9.3 | /28 |
| DNS server | 105.88.9.2 | /28 |
| DHCP server | 105.88.8.66 | /26 |

---

## Services Configured

### DHCP
- Pool name: **T003**
- NET1-A: starts at `105.88.8.75`, max 51 users, gateway `105.88.8.65`, DNS `105.88.9.2`
- NET1-B: wireless devices receive IPs via the same DHCP server through the router relay
- NET2: Central-Office Server acts as DHCP for cell-tower smartphones (range `105.88.8.209–221`)

### Web Server (`www.coe.birzeit.edu` · `105.88.9.4`)
- HTTP and HTTPS enabled
- Custom `index.html` featuring Faculty of Engineering & Technology content, team portfolios, and research areas

### Email (`mail.coe.birzeit.edu` · `105.88.9.3`)
- SMTP and POP3 both enabled
- Domain: `coe.birzeit.edu`
- User accounts created for all team members across Home, Street, and University networks (e.g. `Home1220588`, `University1220782`, `Street1221682`)

### DNS (`dns.coe.birzeit.edu` · `105.88.9.2`)
| # | Name | Type | Detail |
|---|---|---|---|
| 0 | coe.birzeit.edu | CNAME | mail.coe.birzeit.edu |
| 1 | mail.coe.birzeit.edu | A | 105.88.9.3 |
| 2 | www.coe.birzeit.edu | A | 105.88.9.4 |

---

## Routing

OSPF (Process ID 1) is configured on all three routers. Key network-to-area assignments:

```
network 105.88.8.0   0.0.0.3   area 0   ← NET0-A
network 105.88.8.4   0.0.0.3   area 0   ← NET0-B
network 105.88.8.8   0.0.0.3   area 0   ← NET0-C
network 105.88.8.64  0.0.0.63  area 1   ← NET1-A
network 105.88.8.128 0.0.0.63  area 1   ← NET1-B
network 105.88.8.192 0.0.0.31  area 2   ← NET2
network 105.88.8.224 0.0.0.31  area 3   ← NET3
network 105.88.9.0   0.0.0.15  area 4   ← NET4
```

---

## Verification Tests

All cross-network ping and tracert tests passed with 0% packet loss:

- PC0 (NET1-A) → PC1 (NET1-A)
- Smartphone0 (NET1-A) → Laptop0 (NET1-B)
- Tablet PC0 (NET1-B) → PC1 (NET1-A)
- Smartphone1 ↔ Smartphone2 (NET2)
- PC3 ↔ PC2 (NET3)
- Mail server → DNS server (NET4)
- Laptop0 (NET1-B) → DNS server (NET4)
- Laptop0 (NET1-B) → PC2 (NET3)
- PC3 (NET3) → Mail server (NET4)

Web server accessible from PC2, Laptop0, and Smartphone0 via `http://www.coe.birzeit.edu`.
Email successfully exchanged cross-network (e.g. NET1-B Laptop0 → NET3 PC2).

---

## Issues & Solutions

The backbone and cell-tower subnets initially shared an overlapping IP range, preventing the Core network from reaching outside devices. The fix was to split the address space by tightening the backbone subnet mask from `/27` to `/28` (255.255.255.240), giving each segment a distinct, non-overlapping range.

---

## Tools & References

- **Simulator:** Cisco Packet Tracer
- GeeksForGeeks – Computer Networks: https://www.geeksforgeeks.org/computer-networks
- Cisco documentation: https://www.cisco.com
- W. Stallings, *Data and Computer Communications*, 11th ed., Pearson, 2021
