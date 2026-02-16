---
sidebar_position: 1
---

# Powershell 

In questo capitolo verranno percorse le basi di Powershell

## Versioni powershell

||Windows PowerShell 5.1|PowerShell 7|
|-----------|----------------------|------------|
|Piattaforme|Windows|Cross-Platform|
|Sviluppo|Solo security fixes|Versione corrente - attivamente in sviluppo|

## cmdlet

I comandi di Powershell sono chiamati `cmdlet`.
I `cmdlet` normalmente consistono nell'abbinamento di un verbo e un sostantivo sparati da un `-`.

Ad esempio:
```
Get-Help
Write-Host -Object "Hello World!"
```



### I Cmdlet PowerShell Più Comunemente Usati
Questa lista evidenzia alcuni dei **cmdlet PowerShell più frequentemente utilizzati**, basata su guide recenti, raccomandazioni di sysadmin e pattern di utilizzo della community (2025–2026). Questi compaiono costantemente nelle classifiche "top 10/essenziali" e nel lavoro quotidiano di amministrazione e scripting.


| #  | Cmdlet              | Alias comuni         | Cosa fa                                                                 |
|----|---------------------|----------------------|-------------------------------------------------------------------------|
| 1  | Get-Help            | help                 | Mostra aiuto dettagliato, sintassi, esempi e spiegazioni per qualsiasi cmdlet o argomento. Il primo da usare quando si impara o si risolve un problema. |
| 2  | Get-Command         | gcm                  | Elenca tutti i comandi disponibili, funzioni, alias, ecc. Ottimo per scoprire cmdlet cercando per verbo o sostantivo. |
| 3  | Get-ChildItem       | dir, ls, gci         | Recupera file, cartelle, chiavi di registro o elementi in una posizione (simile a `ls` o `dir` in altre shell). |
| 4  | Set-Location        | cd, sl, chdir        | Cambia la directory di lavoro corrente (equivalente a `cd`).           |
| 5  | Get-Process         | gps                  | Elenca i processi in esecuzione sul sistema (nome, ID, uso CPU/memoria, ecc.). |
| 6  | Stop-Process        | kill, spps           | Termina (uccide) uno o più processi in esecuzione per nome o ID.       |
| 7  | Get-Service         | gsv                  | Elenca i servizi Windows e il loro stato (in esecuzione, fermato, ecc.). |
| 8  | Select-Object       | select, sls          | Seleziona proprietà specifiche, conta elementi, salta/prende i primi/ultimi, ecc. |
| 9 | ForEach-Object      | %, foreach           | Esegue un'azione su ogni elemento di una collezione/pipeline (ciclo).  |
| 10 | Export-Csv          | epcsv                | Esporta oggetti in un file CSV (molto usato per report e inventari).   |
| 11 | Out-File            | >, >>, of            | Invia l'output a un file di testo (semplice logging o redirect).       |
| 12 | Get-Member          | gm                   | Mostra proprietà e metodi disponibili su un oggetto (essenziale per esplorare). |
| 13 | Invoke-Command      | icm                  | Esegue comandi/script su computer locali o remoti (remoting potente).  |
| 14 | New-Item            | ni                   | Crea nuovi file, cartelle, chiavi di registro, ecc.                     |



