---
"sidebar_label": "Introduzione"
sidebar_position: 1
title: Introduzione a ExchangeOnlineManagement
pagination_label: Introduzione
---

# Introduzione

## Cos'è ExchangeOnlineManagement?

Il modulo **ExchangeOnlineManagement** (noto anche come **EXO V3**) è lo strumento ufficiale di Microsoft per amministrare **Exchange Online** tramite PowerShell. Exchange Online è il servizio di posta elettronica cloud integrato in Microsoft 365, e questo modulo consente agli amministratori IT di connettersi in modo sicuro e automatizzare operazioni complesse direttamente dalla riga di comando.

È progettato per sostituire le vecchie sessioni remote PowerShell, offrendo un approccio moderno basato su **API REST** che garantisce maggiore sicurezza, prestazioni e affidabilità.

### A cosa serve?
Con ExchangeOnlineManagement puoi gestire:
- **Cassette postali** (mailboxes) e utenti.
- **Gruppi di distribuzione** e di sicurezza.
- **Permessi RBAC** (Role-Based Access Control).
- **Configurazioni di sicurezza e compliance** (es. anti-spam, retention policies).
- **Funzionalità avanzate** come Viva Insights, Workforce Insights e reporting.

### Caratteristiche Principali
- **Autenticazione Moderna**: Supporta OAuth 2.0 e Multi-Factor Authentication (MFA) nativamente.
- **Cmdlet REST-based**: I comandi sono ottimizzati per operazioni bulk, con retry automatici e senza dipendenza da WinRM per Basic Auth.
- **Compatibilità Multi-Piattaforma**: Funziona su Windows (PowerShell 5.1+), Linux e macOS (PowerShell 7+).
- **Cmdlet Esclusivi**: Prefisso `EXO*` per query efficienti (es. `Get-EXOMailbox`).
- **Gestione delle Connessioni**: Supporta connessioni multiple, identità gestite (Managed Identity) e ambienti governativi (GCC, DoD).

### Vantaggi rispetto alle versioni precedenti
- **Prestazioni**: Fino a 10x più veloce per operazioni su larga scala.
- **Sicurezza**: Nessuna esposizione di credenziali; integrazione con Microsoft Entra ID.
- **Manutenzione**: Aggiornamenti automatici via PowerShell Gallery.

**Versione Attuale**: 3.9.2 (GA) – Include nuovi cmdlet per Workforce Insights e parametri come `-EXOModuleBasePath`.

Per approfondire, consulta la [documentazione ufficiale Microsoft](https://learn.microsoft.com/en-us/powershell/exchange/exchange-online-powershell-v2).

