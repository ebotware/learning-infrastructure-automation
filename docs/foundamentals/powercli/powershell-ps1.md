---
sidebar_position: 2
---

# File .ps1
Cos'è un file .ps1, come si struttura, best practice e modalità di esecuzione

## Definizione

Un file **`.ps1`** è un normale file di testo in codifica UTF-8 (o UTF-8 with BOM su Windows classico) che contiene uno o più comandi PowerShell.  
L'estensione `.ps1` indica al sistema operativo (e a PowerShell) che si tratta di uno **script** eseguibile nell'ambiente PowerShell.

PowerShell interpreta ed esegue il contenuto del file riga per riga, dall'alto verso il basso.

## Scopo principale

- Automatizzare attività ripetitive (es. gestione utenti, backup, configurazione server, report)
- Creare funzioni riutilizzabili o piccoli tool
- Orchestrare cmdlet, pipeline, loop, condizioni e chiamate remote
- Sostituire batch (.bat/.cmd) con uno strumento molto più potente e object-oriented

## Struttura tipica di un file .ps1 (best practice 2025–2026)

La community (PowerShell Practice and Style, Microsoft Learn, PoshCode) raccomanda questa struttura per script leggibili e manutenibili:

```powershell
#Requires -Version 7.0          # Specifica versione minima di PowerShell richiesta
#Requires -RunAsAdministrator    # Opzionale: richiede elevazione privilegi

<#
.SYNOPSIS
    Breve descrizione dello scopo dello script (1 riga)

.DESCRIPTION
    Descrizione dettagliata: cosa fa, prerequisiti, esempi di utilizzo.

.PARAMETER Param1
    Descrizione del parametro 1

.PARAMETER Param2
    Descrizione del parametro 2

.EXAMPLE
    .\MyScript.ps1 -Param1 valore -Verbose
    Esegue lo script con logging dettagliato

.NOTES
    Autore: John Doe
    Ultimo aggiornamento: 2026-02
    License: MIT
#>

[CmdletBinding(SupportsShouldProcess, ConfirmImpact='Medium')]
param(
    [Parameter(Mandatory=$true)]
    [string]$NomeUtente,

    [Parameter(Mandatory=$false)]
    [switch]$WhatIf
)

begin {
    # Inizializzazione: variabili globali, import moduli, connessioni
    Write-Verbose "Inizio script - $(Get-Date)"
}

process {
    # Logica principale
    try {
        Get-ADUser -Identity $NomeUtente -ErrorAction Stop
        # ... resto del codice
    }
    catch {
        Write-Error "Errore: $($_.Exception.Message)"
    }
}

end {
    # Cleanup: chiudi connessioni, scrivi log finale
    Write-Verbose "Fine script - $(Get-Date)"
}
```