---
"sidebar_label": "Getting started"
sidebar_position: 2
last_update: 
  date: 03/13/2026
  author: Enrico
---
# Getting started

## Requisiti di Sistema

Prima di iniziare, verifica:
- **PowerShell**: 5.1 (Windows) o 7+ (raccomandato per cross-platform).
- **Account**: Amministratore con ruolo **Exchange Administrator** in Microsoft 365.

## 1. Installazione del Modulo
### Installazione
*  Per l'utente corrente (consigliato)
```powershell
Install-Module -Name ExchangeOnlineManagement -Scope CurrentUser
```
* Per tutti gli utenti (richiede admin)
```powershell
Install-Module -Name ExchangeOnlineManagement
```

### Verifica l'Installazione
```powershell
Get-InstalledModule -Name ExchangeOnlineManagement | Format-List Name, Version
```

### Output atteso:
```
Name                  : ExchangeOnlineManagement
Version               : 3.9.2
```

## 2. Connessione a Exchange Online

### Importa il Modulo
```powershell
Import-Module ExchangeOnlineManagement
```

### Comandi di Connessione

Standard (con login grafico):
  ```powershell
  Connect-ExchangeOnline -UserPrincipalName "tuoemail@tuodominio.com"
  ```

### Test connessione
```powershell
Get-AcceptedDomain
```

Se non ci sono errori, sei connesso!



## 3. Esempi Semplici

### Esempio 1: Elenca le Prime 10 Mailbox
```powershell
# Usa Get-EXO* per prestazioni ottimali
Get-EXOMailbox -ResultSize 10 | Select-Object DisplayName, PrimarySmtpAddress, RecipientTypeDetails
```

### Esempio 2: Dettagli di una Cassetta Postale Specifica
```powershell
Get-EXOMailbox -Identity "utente@tuodominio.com"
```

### Esempio 3: Ottieni informazioni sulla Inbox
Ottenere informazioni su una mailbox, come il numero di email ricevute, lo spazio occupato, la data di creazione ecc...
```powershell
$upn = (Get-ConnectionInformation).UserPrincipalName
Get-EXOMailboxFolderStatistics -Identity $upn -FolderScope Inbox
```

### Esempio 4: Disconnetti la Sessione
```powershell
Disconnect-ExchangeOnline -Confirm:$false
```

Per esempi più complessi, vedi la [guida di connessione ufficiale](https://learn.microsoft.com/en-us/powershell/exchange/connect-to-exchange-online-powershell).