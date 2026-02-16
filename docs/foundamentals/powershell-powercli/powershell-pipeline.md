---
sidebar_position: 2
keywords: [powershell, pipeline, piping, operatore pipe, where-object, select-object, foreach-object]
---

# La Pipeline in PowerShell (Piping)

In Powershell, la **pipeline** (o **piping**) delle istruzioni è una delle funzionalità più potenti e caratteristiche di PowerShell.  
Permette di **collegare** l'output di un comando direttamente come input del comando successivo, creando catene di elaborazione fluide e leggibili in una sola riga.

Invece di passare **testo grezzo** (come fanno `cmd.exe`, bash o zsh), PowerShell passa **oggetti .NET completi** lungo la pipeline → questo rende tutto molto più preciso e ricco di informazioni.

## Sintassi di base

Per definire una pipeline, viene usato il simbolo **pipe** `|`:

```powershell
Comando1 | Comando2 | Comando3 | ...
```

Esempio:

```powershell
Get-Process          # Output: oggetti Process
| Where-Object CPU -gt 100   #Mostra solo i processi che hanno consumato più di 100 secondi di tempo CPU totale dall'avvio
| Sort-Object CPU -Descending  # Ordina dal più "pesante"
| Select-Object -First 5     # Prendi i primi 5
| Format-Table Name, Id, CPU -AutoSize

Get-Process | Where-Object CPU -gt 100 | Sort-Object CPU -Descending| Select-Object -First 5 | Format-Table Name, Id, CPU -AutoSize
```

Risultato: vengono mostrati in una tabella solo i 5 processi che consumano di più CPU.

## Come funziona davvero la pipeline (meccanismo interno)

PowerShell elabora la pipeline in **due modalità principali** di binding (collegamento input): **ValueFromPipeline** e **ValueFromPipelineByPropertyName**.

Per ulteriori informazioni vedere: https://learn.microsoft.com/en-us/powershell/scripting/learn/deep-dives/visualize-parameter-binding?view=powershell-7.5

## Cmdlet più usati nella pipeline

Cmdlet più usati e noti:

| Cmdlet             | Alias comuni     | Scopo principale nella pipeline                  | 
|--------------------|------------------|--------------------------------------------------|
| Where-Object       | `?`, `where`     | Filtrare oggetti                                 |
| Select-Object      | `select`         | Selezionare/rinominare proprietà, prendere primi/ultimi | `... |
| ForEach-Object     | `%`, `foreach`   | Eseguire azione su ogni oggetto                 |
| Sort-Object        | `sort`           | Ordinare per proprietà                          |
| Group-Object       | `group`          | Raggruppare oggetti                             |
| Measure-Object     | `measure`        | Calcolare somme, medie, conteggi                |

## Esempi

Alcuni esempi da usare come referenza

1. **Trova e ferma processi intensivi**

   ```powershell
   Get-Process | Where-Object WorkingSet64 -gt 1GB | Sort-Object WorkingSet64 -Descending | Select Name, Id, @{N='RAM GB';E={[math]::Round($_.WorkingSet64/1GB,2)}}
   ```

2. **Elenca file grandi > 100 MB e cancellali (con WhatIf per sicurezza)**

   ```powershell
   Get-ChildItem C:\Temp -Recurse -File |
       Where-Object Length -gt 100MB |
       Sort-Object Length -Descending |
       Select FullName, @{N='Size MB';E={[math]::Round($_.Length/1MB,1)}} |
       ForEach-Object { Write-Host "Eliminerei: $($_.FullName)" -ForegroundColor Yellow }
   # Per cancellare davvero: rimuovi il Write-Host e aggiungi | Remove-Item -WhatIf
   ```

3. **Report servizi fermi in un file CSV**

   ```powershell
   Get-Service |
       Where-Object Status -eq Stopped |
       Select DisplayName, Name, StartType |
       Export-Csv -Path "$env:USERPROFILE\Desktop\ServiziFermati.csv" -NoTypeInformation -Encoding UTF8
   ```
