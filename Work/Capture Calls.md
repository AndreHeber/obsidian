# on Kamailio

```
apk add kamailio-sipdump
```

```
...
loadmodule "sipdump.so"
...
# enable pcaps with daily rotation saved in /var/log/kamailio-pcaps/
modparam("sipdump", "enable", 1);
modparam("sipdump", "mode", 4);
modparam("sipdump", "rotate", 86400);
modparam("sipdump", "folder", "/var/log/kamailio-pcaps")
```

To display calls in terminal:
```
sngrep -c -I /var/log/kamailio-pcaps/kamailio-sipdump-2022-11-18--16-39-34.pcap
```

# on ACP

- install pcapsipdump from:
http://repo.okay.com.mx/centos/7/x86_64/release/pcapsipdump-0.2-1.el7.x86_64.rpm

```
rpm -i pcapsipdump-0.2-1.el7.x86_64.rpm
```

- run pcapsipdump with
```
/usr/sbin/pcapsipdump -i ens192
```
pcaps are under /var/spool/pcapsipdump/...

- install sngrep with:
```
yum install libpcap-devel
cd /usr/src
git clone https://github.com/irontec/sngrep.git
cd sngrep
./bootstrap.sh
./configure
make
make install (as root)
```

To display calls in terminal:
```
sngrep -c -I /var/spool/pcapsipdump/...
```

# Call Analyser
- central server with access to kamailios & acps
- get unique_id -> call date, call counter & acp
- should display logs from acp & pcaps from acp & kamailios
- preparations:
	- better logging for asterisk: C-[call_counter] must be there for every log line associated to a call
	- install pcap creation on each asterisk & kamailio
- idea: for asterisk logging a terminal window and for each pcap a terminal window with sngrep