Der erste Call ist betroffen, der zweite nicht.
Der Anruf hat einen Delay von etwa 5 Sekunden.
Auffällig war auch eine kurzes Replay von etwa 0.5 Sekunden von Beraterseite, also eine Aufnahme von etwa 0.5 Sekunden wurde wiederholt.
```mermaid
sequenceDiagram
Anrufer->>Hello: 
Hello->>Sabienzia: 
Sabienzia->>Berater: 
Berater->>Sabienzia: 
Sabienzia->>Hello: 
Hello->>Anrufer: 
```

Bei folgender Konstellation tritt es aber nicht auf:
```mermaid
sequenceDiagram
Anrufer->>Sabienzia: 
Sabienzia->>Berater: 
Berater->>Sabienzia: 
Sabienzia->>Anrufer: 
```
