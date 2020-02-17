# Mobility Master Redundancy

The first task that we are going to perform is going to be to cluster our Mobility Master Systems. This breaks down into two configuration steps:

1. Setting up VRRP between the Mobility Masters
2.Setting up Database Replication between the Mobility Masters

## Setting up VRRP

You'll want to select another IP in the management vlan that your Mobility Masters are in to act as the VIP between the two mobility masters. In my case I'm going to be using 10.1.11.62.

### Configuraiton

```
(mm00) [mynode] (config) #vrrp 1
(mm00) ^[mynode] (config-submode)#ip address 10.1.11.62
(mm00) ^[mynode] (config-submode)#description primary-mm00
(mm00) ^[mynode] (config-submode)#authentication aruba123
(mm00) ^[mynode] (config-submode)#no preempt
(mm00) ^[mynode] (config-submode)#priority 120
(mm00) ^[mynode] (config-submode)#advertise 5
(mm00) ^[mynode] (config-submode)#vlan 11
(mm00) ^[mynode] (config-submode)#no shutdown
(mm00) ^[mynode] (config-submode)#exit
(mm00) ^[mynode] (config) #write mem
```

```
(mm01) [mynode] (config) #vrrp 1
(mm01) ^[mynode] (config-submode)#ip address 10.1.11.62
(mm01) ^[mynode] (config-submode)#description secondary-mm01
(mm01) ^[mynode] (config-submode)#authentication aruba123
(mm01) ^[mynode] (config-submode)#no preempt
(mm01) ^[mynode] (config-submode)#priority 100
(mm01) ^[mynode] (config-submode)#advertise 5
(mm01) ^[mynode] (config-submode)#vlan 11
(mm01) ^[mynode] (config-submode)#no shutdown
(mm01) ^[mynode] (config-submode)#exit
(mm01) ^[mynode] (config) #write mem
```

### Verification

We can verify VRRP using the following commands. Ensure that the system with the higher priority is master

```
(mm00) [mynode] (config) #show vrrp stats all
(mm00) [mynode] (config) #show vrrp 1
```

## Configure Database Replication

Next we are going to setup Database Replication between the two Mobility Masters.

### Configuration

First we are going to configure the database synchronization period on the root MM node.

```
(mm00) [mm] (config) #database synchronize period 30
(mm00) ^[mm] (config) #write mem
```

Then we are going to configure the replication on each Mobility Master

```
(mm00) [mm] (config) #cd mm00
(mm00) [mynode] (config) #master-redundancy
(mm00) ^[mynode] (config-submode)#master-vrrp 1
(mm00) ^[mynode] (config-submode)#peer-ip-address 10.1.11.61 ipsec Aruba123
(mm00) ^[mynode] (config-submode)#exit
(mm00) ^[mynode] (config) #write mem
```

```
(mm01) [mynode] (config) #master-redundancy
(mm01) ^[mynode] (config-submode)#master-vrrp 1
(mm01) ^[mynode] (config-submode)#peer-ip-address 10.1.11.60 ipsec Aruba123
(mm01) ^[mynode] (config-submode)#write mem
```

### Verification

Now we are going to verify that our Mobility Masters are replicating as expected using the following commands

```
(mm01) [mynode] #show master-redundancy
(mm00) [mynode] #show database synchronize
```

If the database haven't sychronized yet you can kick off a sync using

```
(mm00) [mynode] #database-synchronize
```

Then check back in again on both nodes by running

```
(mm00) [mynode] #show database synchronize
```

Once you verify that the database are synchronized, you can test by logging into the VIP of the Mobility Masters

```
omega :: ~ » ssh admin@10.1.11.62
admin@10.1.11.62's password:

(mm00) [mynode] #show switches

All Switches
------------
IP Address  IPv6 Address  Name  Location          Type     Model       Version        Status  Configuration State  Config Sync Time (sec)  Config ID
----------  ------------  ----  --------          ----     -----       -------        ------  -------------------  ----------------------  ---------
10.1.11.60  None          mm00  Building1.floor1  master   ArubaMM-VA  8.6.0.2_73853  up      UPDATE SUCCESSFUL    0                       2
10.1.11.61  None          mm01  Building1.floor1  standby  ArubaMM-VA  8.6.0.2_73853  up      UPDATE SUCCESSFUL    0                       2

Total Switches:2
```

As you can see above, the VIP is functional and both Mobility Masters are showing functional based on a `show switches` command.

## DONE!