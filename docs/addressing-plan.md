# Addressing Plan

Consolidated from the README and the topology screenshot. Use it as a single reference when checking configurations.

## VLAN subnets

| VLAN | Network | Subnet | Usable range | Gateway | Mask | Method |
|---|---|---|---|---|---|---|
| 40 | Student Lab | `10.0.0.0/25` | .1 to .126 | `10.0.0.1` | 255.255.255.128 | DHCP |
| 30 | Faculty | `10.0.0.128/27` | .129 to .158 | `10.0.0.129` | 255.255.255.224 | DHCP |
| 60 | Innovation Lab | `10.0.0.160/27` | .161 to .190 | `10.0.0.161` | 255.255.255.224 | DHCP |
| 10 | Administration | `10.0.0.192/27` | .193 to .222 | `10.0.0.193` | 255.255.255.224 | DHCP |
| 20 | Accounts | `10.0.0.224/28` | .225 to .238 | `10.0.0.225` | 255.255.255.240 | DHCP |
| 50 | Server Room | `10.0.0.240/28` | .241 to .254 | `10.0.0.241` | 255.255.255.240 | Static |

The six subnets are contiguous and do not overlap; together they fill `10.0.0.0/24`. The gateway is the first address in each subnet and takes the first usable address from its DHCP pool, which matches the pools seen in `show ip dhcp pool`.

## Router links (/30)

| Link | Subnet | Side A | Side B |
|---|---|---|---|
| Main to Central | `10.0.1.0/30` | Main `10.0.1.1` | Central `10.0.1.2` |
| Central to Innovation | `10.0.1.4/30` | Central `10.0.1.5` | Innovation `10.0.1.6` |
| Central to ISP | `10.0.1.8/30` | Central `10.0.1.9` | ISP `10.0.1.10` |
| Main to Innovation | `10.0.1.12/30` | Main `10.0.1.13` | Innovation `10.0.1.14` |
| ISP to External | `200.0.0.0/30` | ISP `200.0.0.1` | External PC `200.0.0.2` |

## Servers (VLAN 50, static)

| Server | Private IP | Public (static NAT) |
|---|---|---|
| DNS | `10.0.0.242` | `203.0.113.4` |
| Web | `10.0.0.243` | `203.0.113.3` |
| FTP | `10.0.0.244` | none |
| Email | `10.0.0.245` | none |

## DNS and names

- Zone entry: `www.mostafa.com` -> `203.0.113.3`
- External PC DNS server: `203.0.113.4`

## NAT summary

- **Inside:** campus-facing interfaces. **Outside:** `GigabitEthernet0/1` on Central (`10.0.1.9`).
- **PAT:** `access-list 1 permit 10.0.0.0 0.0.0.255` with `overload` on the outside interface.
- **Static NAT:** the two server mappings above.
