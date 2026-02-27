---
sidebar_position: 8
---

# Copiare file ed eseguire comandi remoti nelle VM (Guest Operations)

Questi cmdlet sfruttano **VMware Tools** per interagire direttamente con il sistema operativo guest della VM, **senza bisogno di rete** (né RDP, né SSH, né condivisione file).

**Requisiti obbligatori**:
- VMware Tools installati e funzionanti nella VM
- La VM deve essere **accesa**
- Credenziali guest valide (utente con permessi sufficienti)

Tutti i comandi presuppongono connessione attiva a vCenter/ESXi (`Connect-VIServer`).

## Preparare le credenziali guest

```powershell
# Chiede utente e password guest (consigliato per sicurezza)
$gc = Get-Credential -Message "Credenziali guest OS della VM"

# Oppure inline (solo per test, non in produzione!)
$gc = New-Object System.Management.Automation.PSCredential("Administrator", (ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force))
```

## Copiare file da/verso la VM guest

### Da host locale → VM guest (`LocalToGuest`)

```powershell
# Copia un file singolo
Copy-VMGuestFile `
    -VM "my-vm" `
    -Source "C:\temp\setup.exe" `
    -Destination "C:\Install\setup.exe" `
    -LocalToGuest `
    -GuestCredential $gc `
    -Confirm:$false

# Copia una cartella intera (usa -Recurse se necessario, ma attenzione al tempo/spazio)
Copy-VMGuestFile `
    -VM "my-vm" `
    -Source "C:\Scripts\*" `
    -Destination "C:\Scripts" `
    -LocalToGuest `
    -GuestCredential $gc
```

### Da VM guest → host locale (`GuestToLocal`)

```powershell
# Esempio: scarica un log dalla VM
Copy-VMGuestFile `
    -VM "my-vm" `
    -Source "C:\Windows\Temp\error.log" `
    -Destination "C:\Backup\error-myvm.log" `
    -GuestToLocal `
    -GuestCredential $gc `
    -Confirm:$false
```

> **Note importanti su Copy-VMGuestFile**  
> - Supporta solo **file singoli** o pattern semplici (non wildcard complessi)  
> - Non sovrascrive file esistenti per default → usa `-Force` se necessario  
> - Percorsi guest **case-sensitive** su Linux  
> - Limite pratico: file molto grandi (> pochi GB) possono essere lenti

## Eseguire comandi/script remoti nella VM

Il cmdlet principale è `Invoke-VMScript`. Restituisce l'output del comando eseguito nel guest.

### Esempi base

```powershell
# Windows - PowerShell
Invoke-VMScript `
    -VM "my-vm" `
    -GuestCredential $gc `
    -ScriptText "Get-ChildItem C:\Temp -File | Select Name, Length" `
    -ScriptType PowerShell

# Windows - CMD / batch
Invoke-VMScript `
    -VM "my-vm" `
    -GuestCredential $gc `
    -ScriptText "ipconfig /all" `
    -ScriptType Bat

# Linux - Bash
Invoke-VMScript `
    -VM "my-linux-vm" `
    -GuestCredential $gc `
    -ScriptText "df -h && free -h" `
    -ScriptType Bash
```

### Script PowerShell multilinea (here-string)

```powershell
$script = @'
Write-Output "Inizio script remoto"
Get-Service | Where-Object { $_.Status -eq "Running" } | Select Name, Status -First 5
Write-Output "Fine script"
'@

Invoke-VMScript `
    -VM "my-vm" `
    -GuestCredential $gc `
    -ScriptText $script `
    -ScriptType PowerShell
```

### Catturare output in variabile

```powershell
$result = Invoke-VMScript -VM "my-vm" -GuestCredential $gc -ScriptText "hostname; Get-Date" -ScriptType PowerShell
$result.ScriptOutput   # Output testuale
```

> **Avvertenze**  
> - Timeout predefinito: 100 secondi → usa `-ServerTimeout` per aumentarlo  
> - Su Linux: credenziali devono essere di un utente con shell Bash  
> - Output può essere troncato se troppo lungo → testa con script brevi  
> - Non supporta interattività (no prompt, no sudo password richiesta)