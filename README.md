# Aruba Mobility

These are my configuration and other notes when studying for the ACMX. A lot of this content is based on information available in training courses from a number of sources cut down to what I care about and organized in a way that makes sense.

## Infrastructure

For further reference in documentation I'm not going to scrub my configuration commands and setup of LAN IP's or Hostnames. Please keep in mind when you see IP's or Names that they likely relate back to my infrastructure setup

My infrastructure setup is as follows:

| Mobility Masters | Pair of Clustered Virtual MM's |
| --- | --- |
| mm00.home.byitkc.com | 10.1.11.60 |
| mm01.home.byitkc.com | 10.1.11.61 |
| mm.home.byitkc.com (Cluster VIP) | 10.1.11.62 |

| Mobility Controllers | A pair of Aruba 7010's |
| --- | --- |
| mc00.home.byitkc.com | 10.1.255.14 |
| mc01.home.byitkc.com | 10.1.255.15 |

| VLAN Name | VLAN ID |
| --- | --- |
| workstations | 10 |
| servers | 11 |
| management | 255 |