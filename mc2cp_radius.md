# Radius Configuraiton

## Summary

Here we will look at setting up the integration between the Mobility Controllers and a Clearpass Server. This will cover RADIUS authenticaiton and how to prepare from 802.1x.

I'm not going to cover the setup of the Clearpass System here and this document assumes that you already have the required service configured in Clearpass.

## Configuration

### Configure RADIUS Server for Managed Devices

First, we are going to configure our RADIUS server at the Managed Device (/md) level.

```
(mm00) [mm] (config) #cd /md
(mm00) [md] (config) #aaa authentication-server radius clearpass
(mm00) ^[md] (RADIUS Server "clearpass") #key radius-secret
(mm00) ^[md] (RADIUS Server "clearpass") #host 10.1.11.66
(mm00) ^[md] (RADIUS Server "clearpass") #exit
(mm00) ^[md] (config) #write mem
```

### Configure RADIUS Server Group

```
(mm00) [md] (config) #aaa server-group sg-clearpass
(mm00) ^[md] (Server Group "sg-clearpass") #auth-server clearpass position 1
(mm00) ^[md] (Server Group "sg-clearpass") #write mem
```

This will push down to all devices under the /md, which includes all of our systems. If we wanted to use different RADIUS servers for different Folders under /md we would configure this at that level. At this point, RADIUS will work but the incorrect NAD IP is reported.

### Setting RADIUS IP per Mobility Controller

#### Mobility Controller #0

```
(mm00) [md] (config) #cd mc00
(mm00) [20:4c:03:05:01:e0] (config) #ip radius nas-ip 10.1.255.14
(mm00) ^[20:4c:03:05:01:e0] (config) #write mem
```

#### Mobility Controller #1

```
(mm00) [md] (config) #cd mc01
(mm00) [20:4c:03:1d:c2:ec] (config) #ip radius nas-ip 10.1.255.15
(mm00) ^[20:4c:03:1d:c2:ec] (config) #write mem
```

## Testing

Now we can test our authenticaiton to ensure that it makes to it our Clearpass server without issue. Note that this will likely fail based on the fact that it's not an 802.1x authenticaiton attempt and our service is likely matching that.

```
(mm00) [mynode] #cd mc00
(mm00) [20:4c:03:05:01:e0] #mdc
(mc00) [MDC] #aaa test-server pap clearpass test1 TGS4fun!
```

You'll want to check your Clearpass server at this point and ensure that you see the entry hit Access Tracker and that there are not any issues with the pre-shared secret.