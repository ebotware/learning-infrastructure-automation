---
sidebar_position: 6
---

# Spegnere / Avviare / Riavviare una VM

Tutti i comandi mostrati presuppongono l'uso di **PowerCLI** di VMware e che si sia già connessi a vCenter/ESXi con `Connect-VIServer`.

## Avviare una VM

```powershell
# Avvia una singola VM
Get-VM -Name "myvm" | Start-VM

# Oppure forma più diretta
Start-VM -VM "myvm"
```

## Riavviare una VM

Esistono due approcci principali: **graceful** (consigliato) e **hard** (da usare solo se necessario).

- **Opzione consigliata** – Riavvio pulito del sistema operativo guest  
  (richiede **VMware Tools** installati e funzionanti nella VM)

  ```powershell
  Restart-VMGuest -VM "myvm"
  # oppure con pipeline
  Get-VM -Name "myvm" | Restart-VMGuest
  ```

- **Opzione hard** – Reset brutale della VM  
  (equivalente a premere il pulsante di reset fisico – **sconsigliato** se non strettamente necessario)

  ```powershell
  Restart-VM -VM "myvm"
  # oppure
  Get-VM -Name "myvm" | Restart-VM
  ```

> **Nota**: `Restart-VMGuest` invia il comando di riavvio al sistema operativo guest (come farebbe un `shutdown /r` da dentro Windows).  
> `Restart-VM` invece forza un reset a livello di hypervisor.

## Spegnere una VM

Anche qui esistono due approcci: **graceful** (fortemente consigliato) e **hard**.

- **Opzione consigliata** – Spegnimento pulito del sistema operativo guest  
  (richiede **VMware Tools** installati e funzionanti)

  ```powershell
  Stop-VMGuest -VM "myvm"
  # oppure con pipeline
  Get-VM -Name "myvm" | Stop-VMGuest

  # Alias equivalenti (stesso identico comando)
  Shutdown-VMGuest -VM "myvm"
  ```

- **Opzione hard** – Spegnimento brutale della VM  
  (equivalente a staccare la corrente – **da usare solo se la VM è bloccata**)

  ```powershell
  Stop-VM -VM "myvm"
  # oppure
  Get-VM -Name "myvm" | Stop-VM
  ```

> **Importante**  
> `Stop-VMGuest` / `Shutdown-VMGuest` non aspetta che la VM sia effettivamente spenta.  
> Se devi eseguire comandi solo dopo lo spegnimento completo, aggiungi un ciclo di attesa:

```powershell
$vm = Get-VM -Name "myvm"
Stop-VMGuest -VM $vm -Confirm:$false

while ($vm.ExtensionData.Runtime.PowerState -ne "poweredOff") {
    Write-Host "Attendo spegnimento di $($vm.Name)..."
    Start-Sleep -Seconds 5
    $vm = Get-VM -Name $vm.Name
}
Write-Host "VM spenta correttamente."
```

### Riepilogo rapido (best practice)

| Azione               | Metodo consigliato               | Comando principale             | Richiede VMware Tools? | Quando usarlo                     |
|----------------------|----------------------------------|--------------------------------|------------------------|------------------------------------|
| Avvio                | —                                | `Start-VM`                     | No                     | Sempre                             |
| Riavvio pulito       | Graceful                         | `Restart-VMGuest`              | **Sì**                 | Quasi sempre                       |
| Riavvio forzato      | Hard reset                       | `Restart-VM`                   | No                     | Solo se guest non risponde         |
| Spegnimento pulito   | Graceful                         | `Stop-VMGuest` / `Shutdown-VMGuest` | **Sì**            | Quasi sempre                       |
| Spegnimento forzato  | Hard power-off                   | `Stop-VM`                      | No                     | Solo se guest bloccato             |