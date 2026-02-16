---
sidebar_position: 3
---


# PowerCLI
L'interfaccia di comando per VMware vSphere

## Cos'è PowerCLI?

**PowerCLI** (ora spesso chiamato **VCF PowerCLI** dopo il rebranding Broadcom/VMware) è il tool ufficiale di automazione e gestione command-line per gli ambienti **VMware vSphere**, **VMware Cloud Foundation (VCF)** e prodotti correlati.

Si tratta di una collezione di **moduli PowerShell** sviluppati da VMware/Broadcom che aggiungono **oltre 7000 cmdlet** specializzati per:

- Gestione di vCenter Server e ESXi hosts
- Automazione di VM, datastore, cluster, reti (vSwitch, NSX), storage (vSAN), host profiles, ecc.
- Reportistica, provisioning, configurazione bulk, monitoraggio e disaster recovery
- Integrazione con vRealize, Horizon, NSX-T, Tanzu e altro

PowerCLI trasforma PowerShell in uno strumento potentissimo per sysadmin e DevOps che lavorano con VMware: tutto ciò che fai nel vSphere Client (Web) lo puoi fare via script, in modo ripetibile, veloce e scalabile.

**Versione attuale (2026)**: VCF.PowerCLI 9.x (o VMware.PowerCLI 13.3 come legacy/deprecato).  
Il modulo legacy **VMware.PowerCLI** è deprecato → si raccomanda **VCF.PowerCLI** per ambienti moderni.

## Prerequisiti

- **PowerShell** 7+ (consigliato) o PowerShell 5.1 (Windows integrato)
  - Su Windows → già presente o scarica da GitHub
  - Su Linux/macOS → installa PowerShell Core (`pwsh`)
- Connessione Internet per installazione online (o offline con ZIP)
- Permessi di lettura/scrittura sui moduli PowerShell
- Account con privilegi su vCenter (almeno Read-Only per test)

## Come installare PowerCLI

### Metodo consigliato: Installazione online da PowerShell Gallery (più semplice)

Aprire PowerShell (o pwsh) come amministratore o utente normale:

```powershell
# Imposta policy di esecuzione (solo se necessario, una volta sola)
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# Installa il modulo attuale (VCF PowerCLI)
Install-Module -Name VCF.PowerCLI -Scope CurrentUser -AllowClobber -Force

# Oppure la versione legacy (se serve compatibilità vecchia)
# Install-Module -Name VMware.PowerCLI -Scope CurrentUser -AllowClobber -Force
```

- `-Scope CurrentUser` → installa solo per te (no admin rights necessari)
- `-AllowClobber` → sovrascrive eventuali conflitti
- `-Force` → salta conferme

### Metodo offline (air-gapped / senza Internet)

1. Su una macchina con Internet:
   ```powershell
   Save-Module -Name VCF.PowerCLI -Path C:\Temp\PowerCLI
   ```
2. Copia la cartella in C:\Temp\PowerCLI sulla macchina target
3. Sulla macchina target:
   ```powershell
   Copy-Item -Path "C:\Temp\PowerCLI\VCF.PowerCLI" -Destination "$env:USERPROFILE\Documents\PowerShell\Modules" -Recurse -Force
   ```

Oppure scarica il .zip ufficiale da:  
https://developer.broadcom.com/tools/vcf-powercli/latest/

### Verifica installazione

```powershell
Get-Module -Name VCF.PowerCLI -ListAvailable
# oppure
Get-Module -Name VMware.PowerCLI -ListAvailable
```

## Primi comandi – Getting Started

1. **Connettersi a vCenter Server** (obbligatorio prima di quasi tutto)

   ```powershell
   Connect-VIServer -Server vcenter.miodominio.local -User tuo-utente@dominio -Password tua-password
   # O con prompt credenziali (più sicuro)
   Connect-VIServer -Server vcenter.miodominio.local
   ```

   Esempi avanzati:
   ```powershell
   $cred = Get-Credential
   Connect-VIServer -Server vcsa01.lab.local -Credential $cred -Protocol https
   ```

2. **Elencare tutte le VM** (il "Get-Process" di VMware)

   ```powershell
   Get-VM
   # Con filtri
   Get-VM -Name "web*"
   Get-VM | Where-Object { $_.PowerState -eq "PoweredOn" }
   ```

3. **Informazioni dettagliate su una VM**

   ```powershell
   Get-VM -Name mia-vm | Format-List *
   # O seleziona proprietà
   Get-VM mia-vm | Select Name, PowerState, NumCPU, MemoryGB, Guest.OSFullName, VMHost
   ```

4. **Elencare host ESXi**

   ```powershell
   Get-VMHost | Select Name, State, ConnectionState, Version, Build
   ```

5. **Elencare datastore**

   ```powershell
   Get-Datastore | Select Name, CapacityGB, FreeSpaceGB, @{N="UsedGB";E={$_.CapacityGB - $_.FreeSpaceGB}}
   ```

6. **Disconnettersi**

   ```powershell
   Disconnect-VIServer -Server * -Confirm:$false
   ```

7. **Cercare un comando**

   ```powershell
   Get-Command -Name *Snapshot* -Module "VMware*"
   ```
