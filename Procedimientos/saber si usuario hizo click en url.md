```powershell
UrlClickEvents
| where Url contains "valosfightnight.com" or Url contains "yesware.com"
| project Timestamp, AccountUpn, Url, ActionType, NetworkMessageId
```

se debe ejecutar en "Búsqueda avanzada" de la consola de microsoft defender
