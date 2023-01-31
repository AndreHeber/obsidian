on alpine, install the following:
```bash
apk add gcompat libstdc++ curl bash git
```

also active tunneling in /etc/ssh/sshd-config:
```
AllowTcpForwarding yes
```

and reboot
