- self-hosted with CapRover on [gotify.futureblog.eu](https://gotify.futureblog.eu/)
- image name: gotify/server:2.1.7
- to push message (via REST):
```bash
curl "https://gotify.futureblog.eu/message?token=<app-token>" -F "title=test3" -F "message=test message to client" -F "priority=8"
```
- to receive messages (via websocket):
```bash
wscat -c wss://gotify.futureblog.eu/stream?token=<client-token>
```
- REST docs:
```
https://gotify.futureblog.eu/docs
```