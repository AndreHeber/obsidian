kamailio.cfg:
```
...
## fork=yes
children=4
auto_aliases=no
alias=tls:devpubpk01.sabienzia.com:443

port=5060
## tls_port_no=5061

listen=10.136.136.206

enable_tls=yes
tcp_accept_no_cl=yes

use_dns_cache = on          # Use KAMAILIO internal DNS cache
use_dns_failover = on       # Depends on KAMAILIO internal DNS cache
dns_srv_loadbalancing = on  #
dns_try_naptr = on          #
dns_retr_time=1             # Time in seconds before retrying a DNS request
dns_retr_no=3               # Number of DNS retransmissions before giving up

# Set protocol preference order - ignore target priority
dns_naptr_ignore_rfc= yes  # Ignore target NAPTR priority
dns_tls_pref=50            # First priority: TLS
dns_tcp_pref=30            # Second priority: TCP
dns_udp_pref=10            # Third priority: UDP

/* life time of TCP connection when there is no traffic
 * - a bit higher than registration expires to cope with UA behind NAT */
tcp_connection_lifetime=3604
tcp_rd_buf_size=16384
...
```

SIP port is 5060, but for TLS, SIP port is 5061 and kamailio enforces another port than the port for unencrypted SIP.

```
...
loadmodule "sl.so"
loadmodule "tls.so"  # added
...
modparam("tls", "config", "/etc/kamailio/tls.cfg")
...
```
kamailio needs access rights

tls.cfg
```
[server:default]
method = TLSv1.2+
cipher_list = HIGH
verify_certificate = no
require_certificate = no
private_key = /etc/kamailio/ssl/2022/com/_sabienzia.com-2022-key_rsa.pem
certificate = /etc/kamailio/ssl/2022/com/_sabienzia.com-2022-crt_rsa_bundle.pem

[client:default]
method = TLSv1.2+
cipher_list = HIGH
verify_certificate = yes
require_certificate = yes
```

```bash
$ ls -l /etc/kamailio/
total 64
-rw-r----- 1 root kamailio 13294 Nov 11 16:31 kamailio.cfg
dr-xr-x--- 3 root kamailio    18 Nov 11 14:05 ssl
-rw-r--r-- 1 root kamailio   363 Nov 11 14:49 tls.cfg
```

```bash
 ls -l /etc/kamailio/ssl/2022/com/
total 16
-rw-r----- 1 root kamailio 3778 Nov 11 14:05 _sabienzia.com-2022-crt_rsa_bundle.pem
-rw-r----- 1 root kamailio 1679 Nov 11 14:05 _sabienzia.com-2022-key_rsa.pem
```

With Auth:

```
#!define WITH_AUTH
...
#!ifdef WITH_AUTH
loadmodule "auth.so"
loadmodule "auth_db.so"
#!endif
...
#!ifdef WITH_AUTH
# ----- auth_db params -----
modparam("auth_db", "db_url", DBURL)
modparam("auth_db", "calculate_ha1", no)
modparam("auth_db", "password_column", "ha1")
modparam("auth_db", "load_credentials", "")
modparam("auth_db", "use_domain", MULTIDOMAIN)
#!endif
...


request_route {
...
#!ifdef WITH_AUTH
        # authentication
        route(AUTH);
#!endif
...
}

...
# IP authorization and user authentication
route[AUTH] {
#!ifdef WITH_AUTH
        if (is_method("REGISTER") || from_uri==myself) {
                # authenticate requests
                if (!auth_check("$fd", "subscriber", "1")) {
                        auth_challenge("$fd", "0");
                        exit;
                }
                # user authenticated - remove auth header
                if(!is_method("REGISTER|PUBLISH"))
                        consume_credentials();
        }
        # if caller is not local subscriber, then check if it calls
        # a local destination, otherwise deny, not an open relay here
        if (from_uri!=myself && uri!=myself) {
                sl_send_reply("403","Not relaying");
                exit;
        }
#!endif
        return;
}
...
```

WebRTC:

```
...
#!define WITH_WEBSOCKETS
#!substdef "!MY_WSS_PORT!443!g"
#!substdef "!MY_IP4_ADDR!10.136.136.207!g"
#!substdef "!MY_WSS4_ADDR!tls:MY_IP4_ADDR:MY_WSS_PORT!g"
...
#!ifdef WITH_WEBSOCKETS
#!ifdef WITH_TLS
listen=MY_WSS4_ADDR
#!ifdef WITH_IPV6
listen=MY_WSS6_ADDR
#!endif
#!endif
#!endif
...
#!ifdef WITH_WEBSOCKETS
loadmodule "rtpengine.so"
loadmodule "xhttp.so"
loadmodule "websocket.so"
loadmodule "http_client.so"
loadmodule "htable.so"
#!endif
...
#!ifdef WITH_WEBSOCKETS
# ----- rtpengine params -----
modparam("rtpengine", "rtpengine_sock", "udp:127.0.0.1:22222")
modparam("rtpengine", "extra_id_pv", "$avp(extra_id)")
modparam("rtpengine", "setid_avp", "$avp(setid)")

modparam("websocket", "keepalive_mechanism", 1)
modparam("websocket", "keepalive_timeout", 15)
modparam("websocket", "keepalive_processes", 1)
modparam("websocket", "keepalive_interval", 1)
modparam("websocket", "ping_application_data", "websocket_ping")
modparam("websocket", "sub_protocols", 1)
modparam("websocket", "cors_mode", 0)
modparam("websocket", "verbose_list", 0)
modparam("websocket", "timer_interval", 1)
modparam("websocket", "rm_delay_interval", 2)

modparam("htable", "htable", "websocket_connections=>size=14;autoexpire=86400;")
modparam("htable", "timer_interval", 20);
#!endif
...


request_route {
...
#!ifdef WITH_WEBSOCKETS
        if (nat_uac_test(64)) {
                # Do NAT traversal stuff for requests from a WebSocket
                # connection - even if it is not behind a NAT!
                # This won't be needed in the future if Kamailio and the
                # WebSocket client support Outbound and Path.
                force_rport();
                if (is_method("REGISTER")) {
                        fix_nated_register();
                } else if (!add_contact_alias()) {
                        xlog("L_ERR", "Error aliasing contact <$ct>\n");
                        sl_send_reply("400", "Bad Request");
                        exit;
                }
        }
#!endif
...
}


route[WITHINDLG] {
...
#!ifdef WITH_WEBSOCKETS
                if ($du == "") {
                        if (!handle_ruri_alias()) {
                                xlog("L_ERR", "Bad alias <$ru>\n");
                                sl_send_reply("400", "Bad Request");
                                exit;
                        }
                }
#!endif
...
}
...

#!ifdef WITH_WEBSOCKETS
onreply_route {
        if ((($Rp == MY_WSS_PORT)
                && !(proto == WSS))) {
                xlog("L_WARN", "SIP response received on $Rp\n");
                drop;
        }

        if (nat_uac_test(64)) {
                # Do NAT traversal stuff for replies to a WebSocket connection
                # - even if it is not behind a NAT!
                # This won't be needed in the future if Kamailio and the
                # WebSocket client support Outbound and Path.
                add_contact_alias();
        }
}

event_route[tm:branch-failure:rtpengine] {
        xlog("L_INFO", "BRANCH FAILED: $sel(via[1].branch) + $T_branch_idx");

        $avp(extra_id) = @via[1].branch + $T_branch_idx;
        rtpengine_delete("via-branch=extra");
}

event_route[xhttp:request] {
        set_reply_close();
        set_reply_no_connect();

#!ifdef WITH_TLS
        if ($Rp != MY_WSS_PORT) {
                xlog("L_WARN", "HTTP request received on $Rp\n");
                xhttp_reply("403", "Forbidden", "", "");
                exit;
        }

        xlog("L_INFO", "HTTP Request Received\n");
#!endif
        
        if ($hu =~ "/up") {
                xhttp_reply("200", "OK", "text/plain", "OK");
                exit;
        }

        if ($hdr(Upgrade) =~ "websocket"
                && $hdr(Connection) =~ "Upgrade"
                && $rm =~ "GET"
        ) {

                # Validate Host - make sure the client is using the correct
                # alias for WebSockets
                if ($hdr(Host) == $null || !is_myself("sip:" + $hdr(Host))) {
                        xlog("L_WARN", "Bad host $hdr(Host)\n");
                        xhttp_reply("403", "Forbidden", "", "");
                        exit;
                }

                # ws_handle_handshake() exits (no further configuration file
                # processing of the request) when complete.
                if (ws_handle_handshake()) {
                        # Optional... cache some information about the
                        # successful connection
                        exit;
                }
        }

        xhttp_reply("404", "Not Found", "", "");
}

event_route[websocket:closed] {
        xlog("L_INFO", "WebSocket connection from $si:$sp has closed\n");

        if(!sht_match_name("websocket_connections", "eq", "$si:$sp")) return;

        $var(registered_address) = $sht(websocket_connections=>$si:$sp);
        sht_rm("websocket_connections", "$si:$sp");

        $var(result) = http_client_query(LOADBALANCER_1 + JARVIS_WEBRTC_PATH, "{\"status\":\"closed\",\"contact_target\":\"$var(registered_address)\",\"external_ip\":\"$si\",\"external_port\":\"$sp\"}", "Content-Type: application/json\nAuthorization: Bearer " + JARVIS_BEARER, "$var(content)");
        if ($var(result) == 200) {
                xlog("L_INFO", "curl: status=closed, contact_target=$var(registered_address), external_ip=$si, external_port=$sp\n");
        } else {
                xlog("L_ERR", "could not send http request to " + LOADBALANCER_1 + JARVIS_WEBRTC_PATH + ", status=closed, contact_target=$var(registered_address), result=$var(result)\n");
                xlog("L_INFO", "fallback to " + LOADBALANCER_2 + JARVIS_WEBRTC_PATH);

                $var(result) = http_client_query(LOADBALANCER_2 + JARVIS_WEBRTC_PATH, "{\"status\":\"closed\",\"contact_target\":\"$var(registered_address)\",\"external_ip\":\"$si\",\"external_port\":\"$sp\"}", "Content-Type: application/json\nAuthorization: Bearer " + JARVIS_BEARER, "$var(content)");
                if ($var(result) == 200) {
                        xlog("L_INFO", "curl: status=closed, contact_target=$var(registered_address), external_ip=$si, external_port=$sp\n");
                } else {
                    xlog("L_ERR", "could not send http request to " + LOADBALANCER_2 + JARVIS_WEBRTC_PATH + ", status=closed, contact_target=$var(registered_address), result=$var(result)\n");
                }
        }
}
#!endif
```

DTLS:
```
...
request_route {
...
        # handle requests within SIP dialogs
        route(WITHINDLG);
...
}
...
route[WITHINDLG] {
...
        if (loose_route()) {
...
                if (is_method("BYE")) {
                        setflag(FLT_ACC); # do accounting ...
                        setflag(FLT_ACCFAILED); # ... even if the transaction fails
                } else if ( is_method("ACK") ) {
                        # ACK is forwarded statelessly
                        route(NATMANAGE);
                } else if ( is_method("NOTIFY") ) {
                        # Add Record-Route for in-dialog NOTIFY as per RFC 6665.
                        record_route();
                }
                route(RELAY);
                exit;
        }

route[NATMANAGE] {
...
		$xavp(r=>$T_branch_idx) = "replace-origin replace-session-connection";

		if (!nat_uac_test("8")) {
                $xavp(r=>$T_branch_idx) = $xavp(r=>$T_branch_idx) + " trust-address";
        }

        if (is_request()) {
                if (!has_totag()) {
                        if (!t_is_failure_route()) {
                                $avp(extra_id) = @via[1].branch + $T_branch_idx;
                                $xavp(r=>$T_branch_idx) = $xavp(r=>$T_branch_idx) + " via-branch=extra";
                        }
                }
        }
        
	    if (is_reply()) {
                $avp(extra_id) = @via[2].branch + $T_branch_idx;
                $xavp(r=>$T_branch_idx) = $xavp(r=>$T_branch_idx) + " via-branch=extra";
        }

		if (isbflagset(FLB_RTPWS)) {
                if ($proto =~ "ws") { # web --> SIP
                        $xavp(r=>$T_branch_idx) = $xavp(r=>$T_branch_idx) + " rtcp-mux-demux DTLS=off SDES-off ICE=remove RTP/AVP";
                } else { # SIP --> web
                        $xavp(r=>$T_branch_idx) = $xavp(r=>$T_branch_idx) + " rtcp-mux-offer generate-mid DTLS-fingerprint=sha-256 DTLS=passive SDES-off ICE=force RTP/SAVPF";
                }
        } else {
                if ($proto =~ "ws") { # web --> web
                        $xavp(r=>$T_branch_idx) = $xavp(r=>$T_branch_idx) + " DTLS=passive ICE=force-relay SDES-off";
                }
        }
        
        xlog("L_INFO", "NATMANAGE branch_id:$T_branch_idx ruri: $ru, method:$rm, status:$rs, extra_id: $avp(extra_id), rtpengine_manage: $xavp(r=>$T_branch_idx)\n");

        rtpengine_manage($xavp(r=>$T_branch_idx));
        ...
}
```

Extensive Logging of every SIP packet:
```
onsend_route_reply = yes
...
request_route {
        xlog("L_INFO", "$Tf: Incoming SIP message from $sut:\n$mb\n");
...
onreply_route {
        xlog("L_INFO", "$Tf: Incoming SIP message from $sut:\n$mb\n");
...
onsend_route {
        xlog("L_INFO", "$TF: Outgoing SIP message to $sndto(sproto):$sndto(ip):$sndto(port):\n$snd(buf)\n");
}
```

## Changes

changed /etc/kamailio/kamailio.conf:
```
#!substdef "!MY_PUBLIC_IP4_ADDR!62.67.234.72!g"
...
branch_route[MANAGE_BRANCH] {
        xlog("L_INFO", "MANAGE_BRANCH: New branch [$T_branch_idx] to $ru\n");

        t_on_branch_failure("rtpengine");
+        route(NATMANAGE);
}

onreply_route[MANAGE_REPLY] {
+        xlog("L_INFO", "$Tf: Incoming SIP message from $sut:\n$mb\n");

        xdbg("incoming reply\n");
        if (has_body("application/sdp")) {
-                rtpengine_manage();
+                $xavp(r=>$T_branch_idx) = "";
+
+                if(search("transport=tls")) {
+                        $xavp(r=>$T_branch_idx) = "replace-origin replace-session-connection media-address=MY_PUBLIC_IP4_ADDR";
+                }
+
+                xlog("L_INFO", "MANAGE_REPLY branch_id:$T_branch_idx ruri: $ru, method:$rm, status:$rs, extra_id: $avp(extra_id), rtpengine_manage: $xavp(r=>$T_branch_idx)\n");
+                rtpengine_manage($xavp(r=>$T_branch_idx));
+        }
}

+route[NATMANAGE] {
+        $xavp(r=>$T_branch_idx) = "";
+
+        if(dst_port==5061) {
+                $xavp(r=>$T_branch_idx) = "replace-origin replace-session-connection via-branch=extra rtcp-mux-demux DTLS=off SDES-off ICE=remove RTP/AVP";
+        }
+        xlog("L_INFO", "NATMANAGE branch_id:$T_branch_idx ruri: $ru, method:$rm, status:$rs, extra_id: $avp(extra_id), rtpengine_manage: $xavp(r=>$T_branch_idx)\n");
+
+        rtpengine_manage($xavp(r=>$T_branch_idx));
+        return;
+}
```
