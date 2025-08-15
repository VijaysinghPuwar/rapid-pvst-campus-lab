# Rapid-PVST Campus Lab (STP + PortFast/BPDU Guard)

Resilient Layer-2 campus built in Cisco Packet Tracer to demonstrate:
- **Rapid-PVST** fast convergence (<10s)
- **Per-VLAN roots** (SW1=root VLAN10, SW2=root VLAN20)
- **Dual-homed access** with alternate/blocked path
- **Edge hardening**: PortFast + BPDU Guard
- **Root Guard** on distro downlinks

## Topology
`SW1 (3560)` — `SW2 (3560)` (Gi0/1 trunk)  
`SW3 (2960)` dual-homed to SW2, cross to SW1  
`SW4 (2960)` dual-homed to SW1, cross to SW2  
`SW5 (2950)` = rogue/test switch on SW4 Fa0/10 (edge)

![Topology](https://media.licdn.com/dms/image/v2/D4D2DAQGseJB0C-p5rw/profile-treasury-image-shrink_800_800/B4DZiwBQABHsAc-/0/1755299774901?e=1755907200&v=beta&t=8nBBbgsuyeU7JEI5yomydS6QJLMuz6a_IFNIxnXIEEM)

## How to run
1. Open `pkt/Rapid-PVST-Lab.pkt` in Packet Tracer **8.x+**.  
2. PCs:
   - PC0: `10.10.10.11/24`
   - PC1: `10.10.10.21/24`
3. Global features are enabled on all switches:
   - `spanning-tree mode rapid-pvst`
   - `spanning-tree portfast default`
   - `spanning-tree portfast bpduguard default`
4. Root placement:
   - `SW1`: `spanning-tree vlan 10 root primary`
   - `SW2`: `spanning-tree vlan 20 root primary`

## Validation steps
- **Trunks:** `show interfaces trunk` (Fa0/21/23/24 and Gi0/1 carry VLANs 10,20).
- **Roots & roles:** `show spanning-tree vlan 10/20`  
  Access switches show **Root FWD** on the preferred uplink and **Altn BLK** on the standby.
- **BPDU Guard:** Plug SW5 into **SW4 Fa0/10** → port goes **err-disabled**.  
  `show interfaces status` (Fa0/10 = err-disabled).
- **Failover timing:** continuous ping PC0→PC1; `shutdown` SW4’s active VLAN10 uplink (Fa0/23).  
  RSTP reconverges **<10s**; pings resume.  
  Screenshots in `/images`.

## Files
- `/configs` — per-device configs (sanitized)
- `/images` — topology + results
- `/pkt` — Packet Tracer file

## Notes
- Some 2960 PT images don’t support EtherChannel; this lab uses **individual trunks**.  
- Replace device models with 3560s if you want to extend to **LACP**.

