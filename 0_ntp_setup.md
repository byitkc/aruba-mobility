# NTP Setup on Mobility Masters

We are going to want to configure NTP on our Mobility Masters to ensure that they clocks are synchronized.

In my lab, my NTP servers are 10.1.11.11 and 10.1.11.12

## Configuration

```
(mm01) [mynode] (config) #ntp server 10.1.11.11
Warning: NTP server(s) updated! It is recommended to reboot the device(s) on /mm/mynode node, as certain time sensitive applications like IKE/IPSec/Certmgr may get impacted.
(mm01) ^[mynode] (config) #ntp server 10.1.11.12
Warning: NTP server(s) updated! It is recommended to reboot the device(s) on /mm/mynode node, as certain time sensitive applications like IKE/IPSec/Certmgr may get impacted.
```

Once we setup the NTP servers on both Mobility Master systems we are going to reload both systems.

```
(mm01) [mynode] #reload
Do you really want to restart the system(y/n): y

System will now restart!
```

## Verification

Once the systems come back up you'll want to verify the NTP Settings

```
(mm00) [mynode] #show ntp servers
```

You should see delay and offsets for all configured NTP servers