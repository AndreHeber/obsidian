to trace network activity of a process:
```shell
strace -p $pid -f -e trace=%network -s 10000
```
