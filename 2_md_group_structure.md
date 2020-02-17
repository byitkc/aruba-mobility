# Managed Device Group Structure

Management Hierarchys which push configuration down to nested groups.

Cannot be renamed in AOS 8.0 or 8.1, can be renamed in AOS 8.2 using `configuration node rename <old-path> <new-path>`

I'm going to create a hierarchy of

- Central
  - KansasCity

as my deployment is only one location at the moment, but more advanced hierarchies can be easly created

## Configuration

```
(mm00) [md] #configure t
(mm00) [md] (config) #configuration node /md/Central
(mm00) [md] (config) #configuration node /md/Central/KansasCity
(mm00) [md] (config) #exit
```

## Verification

We can see our current configuration node Hierarchy using

```
(mm00) [md] #show configuration node-hierarchy

Default-node is not configured. Autopark is disabled.

Configuration node hierarchy
----------------------------
Config Node             Type    Name
-----------             ----    ----
/                       System
/md                     System
/md/Central             Group
/md/Central/KansasCity  Group
/mm                     System
/mm/mynode              System
```

## DONE!