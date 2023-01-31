1. install module:
```bash
apk add kamailio-lua
```

2. in /etc/kamailio/kamailio.cfg:
```
loadmodule "app_lua.so"

modparam("app_lua", "load", "/etc/kamailio/kamailio.lua")
modparam("app_lua", "reload", 1)

```

To reload the lua script, run:
```bash
kamcmd app_lua.reload 0
```
