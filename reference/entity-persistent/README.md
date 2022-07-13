# Entity-Persistent

This script is paid in my tebex website

{% embed url="https://brillance-scripts.tebex.io/package/5193076" %}

## how to use

### server.cfg

Make sure to have this order in server.cfg

```
ensure <your framworks>
ensure oxmysql
ensure Entity-Persistent
```

### Client or Server-side script

```lua
-- place in top of script
local EP = exports['Entity-Persistent']:GetCore()
```
