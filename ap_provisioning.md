# Access Point Provisioning

Here we are going to look at access point provisioning using CPSec.

## Configuration

First we are going to locate our Access Point and it's MAC Address using `show ap database`

Then we are going to add the Access Point to our whitelist using

```
(mm00) [md] (config) #whitelist-db cpsec modify mac-address 00:4e:35:c9:05:88 ap-group Production ap-name ap01 cert-type factory-cert description "Access Point #1" state certified-factory-cert
```

Next we are going to run a `show ap database` again to ensure that the AP is associated and is up

```
(mm00) [mm] (config) #show ap database

AP Database
-----------
Name  Group       AP Type  IP Address   Status     Flags  Switch IP    Standby IP
----  -----       -------  ----------   ------     -----  ---------    ----------
ap01  Production  335      10.1.255.16  Up 8m:43s  U2r    10.1.255.15  0.0.0.0
```

We'll want to make sure we are advertising our employee BSSID by checking the controller that the AP is associated to using the `show ap bss-table` command

```
(mc01) [MDC] #show ap bss-table

fm (forward mode): T-Tunnel, S-Split, D-Decrypt Tunnel, B-Bridge (s-standard, p-persistent, b-backup, a-always), n-anyspot

WAN Link State: Up
cluster (cluster role): U-UAC, A-AAC, sU-Standby UAC, sA-Standby AAC

Aruba AP BSS Table
------------------
bss                ess        port  ip           phy    type  ch/EIRP/max-EIRP  cur-cl  ap name  in-t(s)  tot-t  mtu   acl-state  acl  fm  wan-mode  flags  cluster  datazone
---                ---        ----  --           ---    ----  ----------------  ------  -------  -------  -----  ---   ---------  ---  --  --------  -----  -------  --------
00:4e:35:10:58:90  employee1  N/A   10.1.255.16  a-VHT  ap    161E/127.5/0.0    0       ap01     0        13s    1500  -          2    T   always                    no
00:4e:35:10:58:80  employee1  N/A   10.1.255.16  g-HT   ap    6/127.5/0.0       0       ap01     0        13s    1500  -          2    T   always                    no
```

We'll want to ensure that our VAP is added to our AP Group if the SSID is not adveritising.

```
(mm00) [KansasCity] (config) #ap-group Production
(mm00) ^[KansasCity] (AP group "Production") #virtual-ap employee1
(mm00) ^[KansasCity] (AP group "Production") #exit
(mm00) ^[KansasCity] (config) #write mem
```

Then we'll check again using `show ap bss-table` on the MC that the AP is associated to.

## Validation

### Validate GRE Tunnels

```
(mc01) [MDC] #show datapath tunnel table


Datapath Tunnel Table Entries
-----------------------------
 #          Source       Destination    Prt  Type  MTU   VLAN       Acls                    BSSID          Decaps     Encaps   Heartbeats Flags            EncapKBytes  DecapKBytes
 ------  --------------  --------------  ---  ----  ----  ---- -----------------------  ----------------- ---------- ---------- ---------- --------------- ------------- -----------
 14      SPI541EE600out  10.1.255.16     50   IPSE  1500  0    routeDest 00FF     0                                0        157                              0           0
 18      10.1.255.15     10.1.255.16     47   9000  1500  0    0   0    0    0    0     00:4e:35:c9:05:88        174          0        171 TES
 17      10.1.255.15     10.1.255.16     47   8300  1500  10   0   0    2    0    0     00:4e:35:10:58:80          0          0          0 IMASPab
 9       SPIED51A100 in  10.1.255.15     50   IPSE  1500  0    routeDest 00FF     0                             5200          0            T                 0           0
 13      SPI4EC45A00 in  10.1.255.15     50   IPSE  1500  0    routeDest 00FF     0                              292          0                              0           0
 16      10.1.255.15     10.1.255.16     47   8200  1500  10   0   0    2    0    0     00:4e:35:10:58:90          0          0          0 IMASPab
 10      SPI981B3900out  10.1.11.62      50   IPSE  1500  0    routeDest 00FF     0                                0       6744            T                 0           0
```

We are looking for tunnels that are terminating to 10.1.255.16 from 10.1.255.15. Those are the tunnels from our AP to our Mobility Controller. We'll want to check the flags to ensure that the tunnel is up and functional.

### Validate ESSID's associated with AP

```
(mc01) [MDC] #show ap essid

ESSID Summary
-------------
ESSID      APs  Clients  VLAN(s)  Encryption
-----      ---  -------  -------  ----------
employee1  1    0        10       WPA2 8021X AES
Num ESSID:1
```

Here we can see that our ESSID of employee1 is setup on the single access point that we just setup in our applicable AP Group.