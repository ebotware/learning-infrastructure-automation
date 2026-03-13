---
sidebar_position: 9
---

# Gestione Dischi Virtuali

## Visualizzare dischi esistenti
```powershell
Get-VM -Name vm-name | Get-HardDisk
Get-VM -Name vm-name | Get-HardDisk | Select-Object Name, CapacityGB, Filename, DiskType
```

## Creazione di un nuovo Virtual Disk
Comando per creare un nuovo virtual disk (da testare):

```powershell
# === Script per CREARE un nuovo hard disk su una VM VMware e (opzionalmente) inizializzarlo dentro Windows ===

# 1. Connessione al vCenter (da eseguire se non si è ancora connessi)
# Connect-VIServer vcenter.your-domain.local -Credential (Get-Credential)

# Richiesta credenziali per la VM guest (per Invoke-VMScript più avanti)
$guestCred = Get-Credential -Message "Credenziali amministrative della VM Windows"

# Variabili di configurazione - Da modificare
$vmName         = "my-vm-1"              # Nome della VM target
$diskSizeGB     = 40                     # Dimensione del nuovo disco in GB
$diskFormat     = "Thin"                 # Opzioni: "Thin", "Thick", "EagerZeroedThick"
$datastoreName  = "datastore-ssd-01"     # Nome del datastore dove creare il VMDK (opzionale, altrimenti usa default della VM)
$controllerName = "SCSI Controller 0"    # Controller SCSI esistente (di solito "SCSI Controller 0" o "1")
                                         # Se vuoi un nuovo controller → vedi nota sotto

# ------------------------------------------------------------------------------
# PARTE 1 - Creazione del nuovo hard disk virtuale lato vSphere
# ------------------------------------------------------------------------------

Write-Host "Creazione nuovo hard disk da $diskSizeGB GB sulla VM '$vmName'..."

$vm = Get-VM -Name $vmName -ErrorAction Stop

$params = @{
    VM           = $vm
    CapacityGB   = $diskSizeGB
    StorageFormat = $diskFormat          # Thin = provisioning dinamico (risparmio spazio)
                                         # Thick = provisioning lazy zeroed
                                         # EagerZeroedThick = zeroed subito (migliore per performance/clustering)
}

# Per specificare un datastore specifico
if ($datastoreName) {
    $params.Datastore = Get-Datastore -Name $datastoreName -ErrorAction Stop
}

# Per assegnare un controller specifico
if ($controllerName) {
    $controller = Get-ScsiController -VM $vm | Where-Object { $_.Name -eq $controllerName }
    if ($controller) {
        $params.Controller = $controller
    } else {
        Write-Warning "Controller '$controllerName' non trovato → verrà usato il primo disponibile"
    }
}

# Esecuzione creazione disco
$newDisk = New-HardDisk @params -Confirm:$false -ErrorAction Stop

Write-Host "Nuovo disco creato con successo!"
Write-Host "  - Nome disco: $($newDisk.Name)"
Write-Host "  - Capacità:   $($newDisk.CapacityGB) GB"
Write-Host "  - Formato:    $diskFormat"
Write-Host "  - Percorso:   $($newDisk.Filename)"

# ------------------------------------------------------------------------------
# PARTE 2 - (Opzionale) Inizializzazione, partizionamento e formattazione dentro la VM
# ------------------------------------------------------------------------------

# Script PowerShell da eseguire nella VM guest per:
# - Online il nuovo disco
# - Inizializzare (GPT)
# - Creare partizione primaria
# - Formattare NTFS
# - Assegnare lettera D: (cambia se preferisci)

$initScript = @'
# Script eseguito dentro la VM Windows

# Attendi che il nuovo disco appaia (a volte ci vogliono 10-30 secondi)
Start-Sleep -Seconds 15

# Trova il disco raw (non inizializzato, solito Disk Number più alto)
$rawDisk = Get-Disk | Where-Object { $_.PartitionStyle -eq 'RAW' -and $_.IsOffline -eq $false } | Sort-Object Number -Descending | Select-Object -First 1

if ($rawDisk) {
    Write-Output "Trovato disco raw: Disk $($rawDisk.Number) - $($rawDisk.Size / 1GB) GB"

    # Inizializza come GPT
    Initialize-Disk -Number $rawDisk.Number -PartitionStyle GPT -Confirm:$false

    # Crea partizione primaria usando tutto lo spazio
    $partition = New-Partition -DiskNumber $rawDisk.Number -UseMaximumSize -AssignDriveLetter

    # Formatta NTFS con etichetta "Data"
    Format-Volume -DriveLetter $partition.DriveLetter -FileSystem NTFS -NewFileSystemLabel "Data" -Confirm:$false

    Write-Output "Disco inizializzato, partizionato e formattato come $($partition.DriveLetter): 'Data'"
} else {
    Write-Output "Nessun disco raw trovato. Controlla in Disk Management."
}
'@

# Esecuzione remota nella VM (VM deve essere accesa + VMware Tools OK)
Write-Host "`nInizializzazione disco dentro la VM (Invoke-VMScript)..."

Invoke-VMScript -VM $vmName `
                -ScriptText $initScript `
                -GuestCredential $guestCred `
                -ScriptType PowerShell `
                -ErrorAction Stop

Write-Host "Operazione completata! Controlla Disk Management nella VM per verificare il nuovo volume."
```
## Ridimensionamento partizione 
Per ridimensionare una partizione non è sufficente allargare il disco virtuale. Anche la partizione Windows al suo interno deve essere ingrandita.
```powershell
# === Script per aumentare di 2 GB un disco virtuale su VMware vSphere e ridimensionare la partizione dentro la VM ===

# Richiesta delle credenziali per connettersi al vCenter / ESXi
# (verrà mostrata la finestra di login classica di PowerCLI)
$cred = Get-Credential

# Variabili di configurazione - modificare questi valori secondo necessità
$vmName    = "my-vm-1"           # Nome della macchina virtuale
$diskName  = "Hard disk 1"       # Nome del disco come appare in vSphere (di solito "Hard disk 1", "Hard disk 2", ...)
$increaseBy = 2                  # Di quanti GB vogliamo aumentare il disco

# ------------------------------------------------------------------------------
# PARTE 1 - Aumento della dimensione del disco virtuale lato vSphere
# ------------------------------------------------------------------------------

# Recupera la capacità attuale del disco specificato (in GB)
$currentCapacity = Get-HardDisk -VM $vmName -Name $diskName |
                   Select-Object -ExpandProperty CapacityGB -ErrorAction Stop

# Calcolo della nuova dimensione desiderata
$newCapacity = $currentCapacity + $increaseBy

Write-Host "Disco attuale: $currentCapacity GB → Nuova dimensione: $newCapacity GB"

# Esegue l'aumento del disco (senza chiedere conferma)
Get-HardDisk -VM $vmName -Name $diskName |
    Set-HardDisk -CapacityGB $newCapacity -Confirm:$false -ErrorAction Stop

Write-Host "Disco virtuale aumentato con successo a $newCapacity GB"

# ------------------------------------------------------------------------------
# PARTE 2 - Ridimensionamento della partizione all'interno della VM
# ------------------------------------------------------------------------------

$scriptToRun = @'
# Script da eseguire dentro la VM Windows
$maxSize = (Get-PartitionSupportedSize -DriveLetter C).SizeMax
if ($maxSize -gt 0) {
    Write-Output "Ridimensiono partizione C: alla dimensione massima supportata: $maxSize bytes"
    Resize-Partition -DriveLetter C -Size $maxSize -ErrorAction Stop
    Write-Output "Ridimensionamento completato"
} else {
    Write-Output "Errore: dimensione massima supportata non valida"
}
'@

# Esecuzione remota dello script nella VM
# Nota: la VM deve avere VMware Tools installati e funzionanti
#       e deve esistere un account con permessi amministrativi
Invoke-VMScript -VM $vmName `
                -ScriptText $scriptToRun `
                -GuestCredential $cred `
                -ScriptType PowerShell `
                -ErrorAction Stop
```