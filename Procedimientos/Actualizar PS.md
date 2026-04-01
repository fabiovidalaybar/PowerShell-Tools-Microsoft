```powershell
winget upgrade --id Microsoft.PowerShell
```
En algunos casos el comando anterior no funcionará por que la forma en que instalamos anteriormente PS fue con otra tecnología, en dicho caso, se debe usar el siguiente comando

```powershell
iex "& { $(irm https://aka.ms/install-powershell.ps1) } -UseMSI"
```

<img width="764" height="473" alt="image" src="https://github.com/user-attachments/assets/d0efc8f7-fb4b-4fe2-9ae4-fdec34b4f720" />
