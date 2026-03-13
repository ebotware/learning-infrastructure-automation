---
sidebar_position: 7
---

# Gestione Snapshot delle VM

Gli **snapshot** permettono di catturare lo stato di una VM in un momento preciso (dischi, memoria opzionale, configurazione).  
Sono utili per test, aggiornamenti o rollback rapidi, **ma non sostituiscono i backup** veri e propri.

> **Best practice VMware**  
> • Non tenere snapshot per più di 24–72 ore  
> • Mantenere catene di snapshot corte (max 2–3 livelli consigliati)  
> • Evitare snapshot come soluzione di backup a lungo termine  
> • Monitorare lo spazio su datastore (i delta disk crescono velocemente)

Tutti i comandi presuppongono **PowerCLI** installato e connessione attiva a vCenter/ESXi (`Connect-VIServer`).

## Selezionare la VM

```powershell
# Recupera una VM specifica
$vm = Get-VM -Name "my-vm"

# Oppure direttamente in pipeline
Get-VM -Name "my-vm"
```

## Creare uno snapshot

```powershell
# Snapshot base (consigliato per VM spente o quando non serve quiesce)
Get-VM -Name "my-vm" | New-Snapshot `
    -Name "Prima aggiornamento PHP 8.1" `
    -Description "Catturato prima dell'upgrade a PHP 8.1 - rollback pronto"

# Snapshot con quiesce (molto consigliato per VM accese con dati importanti)
Get-VM -Name "my-vm" | New-Snapshot `
    -Name "Pre-patching 2025-02" `
    -Description "Prima patch di febbraio" `
    -Quiesce

# Snapshot con memoria
Get-VM -Name "my-vm" | New-Snapshot ` -Name "Debug crash" ` -Description "Stato esatto al momento del crash" ` -Memory
```

> **Nota**  
> `-Quiesce` richiede VMware Tools installati e funzionanti.  

## Visualizzare gli snapshot

```powershell
# Lista snapshot di una VM con info utili
$vm | Get-Snapshot | Select-Object VM, Name, Description, Created, SizeGB, Parent, Children

# Tutti gli snapshot di tutte le VM (utile per report)
Get-VM | Get-Snapshot | Select-Object VM, Name, Created, SizeGB | Sort-Object Created -Descending

# Snapshot più vecchi di 60 giorni
$limite = (Get-Date).AddDays(-60)
Get-VM | Get-Snapshot | Where-Object { $_.Created -lt $limite } | Select-Object VM, Name, Created, SizeGB
```

## Eliminare uno snapshot

```powershell
# Elimina snapshot specifico
$vm | Get-Snapshot -Name "Prima aggiornamento PHP 8.1" | Remove-Snapshot -Confirm:$false

# Elimina tutti gli snapshot vecchi di una VM
$vm | Get-Snapshot | Where-Object { $_.Created -lt (Get-Date).AddDays(-30) } | Remove-Snapshot -Confirm:$false

# Elimina in background (più veloce per molti snapshot)
Get-VM -Name "my-vm" | Get-Snapshot | Remove-Snapshot -RunAsync -Confirm:$false
```

> **Attenzione**  
> L’eliminazione di snapshot può richiedere tempo (consolidamento dischi). Non interrompere il processo!  
> Usa `-RemoveChildren` se vuoi eliminare un intero ramo della catena (raro).

## Riepilogo

| Operazione              | Comando consigliato                          | Richiede Tools? | Note importanti                              |
|-------------------------|----------------------------------------------|-----------------|----------------------------------------------|
| Snapshot VM spenta      | `New-Snapshot -Name ...`                     | No              | Veloce e pulito                              |
| Snapshot VM accesa      | `New-Snapshot -Quiesce:$true`                | **Sì**          | Consistenza filesystem – quasi sempre usarlo |
| Snapshot con memoria    | `New-Snapshot -Memory:$true`                 | No              | Snapshot con memoria         |
| Lista snapshot          | `Get-Snapshot | Select VM,Name,Created,SizeGB` | No           | Monitora SizeGB regolarmente                 |
| Rimozione sicura        | `Remove-Snapshot -Confirm:$false`            | No              | Usa `-RunAsync` per batch                    |

Se vengono gestite molte VM, considerare uno script di pulizia automatica (es. rimuovi snapshot > 7–14 giorni) e monitorare lo spazio con `Get-Datastore`.

