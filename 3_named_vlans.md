# Named VLAN Setup

We are going to go through the process of creating our Named VLANS.

| NAME | VLAN |
| --- | --- |
| workstations | 10 |
| servers | 11 |
| management | 255 |
| employees | 999 |

## Configuration

### Commands

```
vlan <vlan-id>
vlan-name <vlan-name>
vlan <vlan-name> <vlan-id>
show vlan
show configuration effective
```

### Setup

First we will setup our Named VLANS at the main /md level, but this can be setup whereever.

```
(mm00) [md] (config) #vlan-name workstations
(mm00) ^[md] (config) #vlan-name servers
(mm00) ^[md] (config) #vlan-name management
(mm00) ^[md] (config) #vlan-name employees
(mm00) ^[md] (config) #exit
(mm00) ^[md] #write mem
```

Then we are going to configure our named VLAN to VLAN ID mapping on the desired Managed Device node (/md/Central/KansasCity)

```
(mm00) [KansasCity] (config) #vlan 10
(mm00) ^[KansasCity] (config-submode)#vlan 11
(mm00) ^[KansasCity] (config-submode)#vlan 255
(mm00) ^[KansasCity] (config-submode)#vlan 999
(mm00) ^[KansasCity] (config-submode)#vlan servers 10
(mm00) ^[KansasCity] (config-submode)#exit
(mm00) ^[KansasCity] (config) #vlan workstations 10
(mm00) ^[KansasCity] (config) #vlan servers 11
(mm00) ^[KansasCity] (config) #vlan management 255
(mm00) ^[KansasCity] (config) #vlan employees 999
(mm00) ^[KansasCity] (config) #write mem   
```

## Verification

We can verify our setup using `show configuration effective | begin vlan` on the node that we want to verify. We can also show inheritance using `show configuration effective detail`

