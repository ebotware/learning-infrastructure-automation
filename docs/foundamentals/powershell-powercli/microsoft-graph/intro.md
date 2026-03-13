---
sidebar_position: 1
---

# Introduzione a Microsoft Graph PowerShell SDK

Microsoft Graph è l'**API unificata** di Microsoft 365 che permette di accedere a praticamente tutti i dati e le funzionalità di:

- Microsoft Entra ID (ex Azure AD)
- Exchange Online
- SharePoint Online & OneDrive
- Teams
- Intune
- Planner
- Outlook
- Calendar
- …e decine di altri servizi Microsoft 365

Invece di dover usare 4-5 moduli diversi (AzureAD, MSOnline, ExchangeOnlineManagement, SharePointPnPPowerShell, ecc.), oggi Microsoft punta tutto su **un unico punto di accesso**: **Microsoft Graph**.

### Cos’è il Microsoft Graph PowerShell SDK?

Il **Microsoft Graph PowerShell SDK** è il modulo ufficiale PowerShell creato da Microsoft che funge da **wrapper** intorno alle API REST di Microsoft Graph.

In pratica trasforma richieste HTTP come:

```http
GET https://graph.microsoft.com/v1.0/users
Authorization: Bearer eyJ0eXAiOiJKV1Qi...
```

in comandi PowerShell molto più leggibili e naturali:
```powershell
PowerShellGet-MgUser -All
````

Principali vantaggi (rispetto ai vecchi moduli)

* Unico modulo → meno confusione e meno dipendenze da gestire
* Copertura quasi totale di Microsoft 365 (v1.0 + beta)
* Cmdlet auto-generati → escono aggiornamenti molto velocemente quando escono nuove funzionalità in Graph
* Sostituto ufficiale di AzureAD e MSOnline (che sono in deprecazione / ritiro)
* Supporta sia autenticazione delegata (utente che esegue) che app-only (identità servizio / managed identity)
* Community e documentazione ufficiale molto attiva