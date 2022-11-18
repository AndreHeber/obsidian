```
Nov 14 15:00:22 BER1-DEV-Peer /usr/sbin/kamailio[29752]: NOTICE: <script>: dialed number: +493022389987
Nov 14 15:00:22 BER1-DEV-Peer /usr/sbin/kamailio[29752]: NOTICE: <script>: dispatcherGroup: 13
Nov 14 15:00:22 BER1-DEV-Peer /usr/sbin/kamailio[29752]: INFO: <script>: MANAGE_BRANCH: New branch [0] to sip:+493022389987@devpubpk01.sabienzia.com:5061;transport=tls
Nov 14 15:00:22 BER1-DEV-Peer /usr/sbin/kamailio[29744]: INFO: <script>: BRANCH FAILED: z9hG4bKL8rVSaXw + 0
Nov 14 15:00:22 BER1-DEV-Peer /usr/sbin/kamailio[29752]: INFO: <script>: NATMANAGE branch_id:0 ruri: sip:+493022389987@devpubpk01.sabienzia.com:5061;transport=tls, method:ACK, status:<null>, extra_id: <null>, rtpengine_manage: replace-origin replace-session-connection trust-address via-branch=extra rtcp-mux-demux DTLS=off SDES-off ICE=remove RTP/AVP
```

```
INFO: <script>: Mon Nov 14 17:26:15 2022: Incoming SIP message from sip:46.19.210.14:6073;transport=tls:
INVITE sip:+493022389987@devpubpk01.sabienzia.com:5061;transport=tls SIP/2.0
Via: SIP/2.0/TLS 46.19.210.14:6073;branch=z9hG4bKfxYilaAD;rport
From: "4915150424601" <sip:4915150424601@46.19.210.14>;tag=13-712C2673-63726C270004B1B2-749BF700
To: <sip:+493022389987@devpubpk01.sabienzia.com:5061>
CSeq: 10 INVITE
Call-ID: 13-13-640BC430-63726C270004B1CB-749BF700
Max-Forwards: 69
Allow: INVITE, ACK, CANCEL, OPTIONS, BYE, UPDATE, INFO
Diversion: <sip:+493022389987@sip.didww.com>;reason=unconditional
User-Agent: DIDWW SBC node
Content-Type: application/sdp
Contact: <sip:46.19.210.14:6073;transport=tls>
Content-Length: 443

v=0
o=didww-sbc 361901320 1 IN IP4 46.19.210.22
s=didww-sbc
t=0 0
m=audio 43968 UDP/TLS/RTP/SAVP 8 0 18 4 101
c=IN IP4 46.19.210.22
a=rtpmap:8 PCMA/8000
a=rtpmap:0 PCMU/8000
a=rtpmap:18 G729/8000
a=fmtp:18 annexb=no
a=rtpmap:4 G723/8000
a=rtpmap:101 telephone-event/8000
a=fingerprint:SHA-256 2B:47:4C:58:66:1C:21:A7:D2:50:09:B5:C1:42:95:36:79:76:CC:70:FE:E0:72:E1:2E:A7:1F:55:71:6A:34:04
a=ptime:20
a=sendrecv
a=setup:actpass
```

```
INFO: <script>: Mon Nov 14 17:26:15 2022: Outgoing SIP message to tls:46.19.210.14:6073:
SIP/2.0 180 Ringing
Call-ID: 13-13-640BC430-63726C270004B1CB-749BF700
From: "4915150424601" <sip:4915150424601@46.19.210.14>;tag=13-712C2673-63726C270004B1B2-749BF700
To: <sip:+493022389987@devpubpk01.sabienzia.com>;tag=d0aefa51-dc5f-4190-8b54-2536b51fee65
CSeq: 10 INVITE
Server: Sabienzia
Contact: <sip:10.136.136.112:5060>
Allow: OPTIONS, SUBSCRIBE, NOTIFY, PUBLISH, INVITE, ACK, BYE, CANCEL, UPDATE, PRACK, MESSAGE
Content-Length:  0
Via: SIP/2.0/TLS 46.19.210.14:6073;received=46.19.210.14;branch=z9hG4bKfxYilaAD;rport=6073
Record-Route: <sip:10.136.136.206;r2=on;lr=on>,<sip:10.136.136.206:5061;transport=tls;r2=on;lr=on>
P-SR-XBranch: z9hG4bK9c69.11c1b7fc9f26abdf8f222f27159211be.0
```

```
INFO: <script>: Mon Nov 14 17:26:19 2022: Outgoing SIP message to tls:46.19.210.14:6073:
SIP/2.0 200 OK
Call-ID: 13-13-640BC430-63726C270004B1CB-749BF700
From: "4915150424601" <sip:4915150424601@46.19.210.14>;tag=13-712C2673-63726C270004B1B2-749BF700
To: <sip:+493022389987@devpubpk01.sabienzia.com>;tag=d0aefa51-dc5f-4190-8b54-2536b51fee65
CSeq: 10 INVITE
Server: Sabienzia
Allow: OPTIONS, SUBSCRIBE, NOTIFY, PUBLISH, INVITE, ACK, BYE, CANCEL, UPDATE, PRACK, MESSAGE
Contact: <sip:10.136.136.112:5060>
Supported: 100rel
Content-Type: application/sdp
Content-Length:   401
Via: SIP/2.0/TLS 46.19.210.14:6073;received=46.19.210.14;branch=z9hG4bKfxYilaAD;rport=6073
Record-Route: <sip:10.136.136.206;r2=on;lr=on>,<sip:10.136.136.206:5061;transport=tls;r2=on;lr=on>
P-SR-XBranch: z9hG4bK9c69.11c1b7fc9f26abdf8f222f27159211be.0

v=0
o=- 361901320 3 IN IP4 10.136.136.112
s=Asterisk
c=IN IP4 10.136.136.206
t=0 0
m=audio 53024 UDP/TLS/RTP/SAVP 8 0 18 101
a=maxptime:150
a=rtpmap:8 PCMA/8000
a=rtpmap:0 PCMU/8000
a=rtpmap:18 G729/8000
a=rtpmap:101 telephone-event/8000
a=fmtp:101 0-16
a=sendrecv
a=rtcp:53025
a=setup:active
a=fingerprint:sha-1 FC:66:E0:B9:39:AF:17:AC:7F:45:70:39:9B:F8:F4:29:91:27:9E:4B
a=ptime:20
```


