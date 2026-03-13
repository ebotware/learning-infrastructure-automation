---
sidebar_position: 2
---

# Getting started

Per iniziare ad usare Microsoft.Graph occorre innanzitutto per prima cosa installare il relativo modulo Powershell:

```powershell
# Installazione per l'utente corrente (più comune e veloce)
Install-Module Microsoft.Graph -Scope CurrentUser -Force

# Oppure per tutti gli utenti del computer (richiede elevazione)
# Install-Module Microsoft.Graph -Scope AllUsers -Force
```

## Primi comandi
Connessione e test base

### 1. Connessione interattiva
Aprire PowerShell → eseguire:
```powershell
PowerShell# Connessione base – apre finestra di login Microsoft
Connect-MgGraph

# Versione consigliata: specifica subito gli scope che ti servono
Connect-MgGraph -Scopes "User.Read.All", "Group.Read.All"
```

* La prima volta ti chiederà di autenticarti con browser
* Accettare i consensi (se si è Global Admin o si hanno i permessi)
* Dopo il login verra visualizzato: Welcome To Microsoft Graph!

### 2. Controllare se si è connessi e quali permessi si hanno

```powershell
# Mostra l'account con cui sei connesso
Get-MgContext | Select-Object Account, Scopes, AuthType, TokenExpiration

# O più semplicemente:
Get-MgContext
```

### 3. Primo comandi e test classici
Mostrare e cercare utenti nel tenant EntraId

```powershell
# Elenca tutti gli utenti (richiede User.Read.All)
Get-MgUser -All -Top 10

# O solo il tuo account
Get-MgUser -UserId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Cerca un utente per nome visualizzato o mail
Get-MgUser -Filter "startswith(displayName,'Tom')" -Top 5

# Elenca i gruppi (richiede Group.Read.All)
Get-MgGroup -Top 10

# Informazioni sul tenant
Get-MgOrganization
```

### 4. Disconnessione
```powershell
Disconnect-MgGraph
```