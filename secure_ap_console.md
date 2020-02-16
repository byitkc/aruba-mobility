# Securing the AP Console

There are many instances where you would want to secure the AP Console port access with a password to ensure that malicious actors cannot hijack your Access Points. We'll configure at our preferred MD node, in this case we will use the root MD node.

```
(mm00) [md] (config) #ap system-profile default
(mm00) ^[md] (AP system profile "default") #ap-console-password aruba123
(mm00) ^[md] (AP system profile "default") #write mem
```