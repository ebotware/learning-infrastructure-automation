---
sidebar_position: 5
---

# PowerCLI Query

## Connessione iniziale

Prima di usare `Get-VM`, bisogna connettersi a un server ESXi o a un vCenter con:

* `Connect-VIServer`

Una volta stabilita la connessione, si può eseguire:

* `Get-VM`

Senza parametri, il comando mostra tutte le macchine virtuali (VM).
In ambienti piccoli l’output è leggibile, ma in produzione (con migliaia di VM) è necessario usare filtri.

## Filtrare con i parametri principali

### -Name

Permette di cercare una VM specifica per nome.

Supporta wildcard:

* `*` → qualsiasi numero di caratteri
* `?` → un solo carattere

Le virgolette sono necessarie solo se il nome contiene spazi.

### -Datastore

Filtra le VM presenti in uno specifico datastore.

### -Location

Accetta diversi oggetti:

* Cluster
* Datacenter
* Host
* Folder
* Resource Pool
* vApp

Per conoscere il nome di un cluster si può usare:

* `Get-Cluster`

### -Tag

Mostra le VM con un determinato tag (che deve essere stato creato e assegnato in precedenza).



## La Pipeline

Uno dei concetti chiave.

Esempio:

```powershell
Get-VM | Select-Object
```

In PowerShell:

* nella pipeline passano **oggetti**, non testo (a differenza di UNIX)


## Select-Object

Serve a scegliere quali proprietà mostrare.

Di default `Get-VM` mostra:

* Name
* PowerState
* NumCPU
* Memory

Ma le VM hanno molte altre proprietà.

Con:

* `Select-Object -Property *`
si visualizzano tutte.

Oppure si possono specificare proprietà precise:

* `Name`
* `Folder`
* `HardwareVersion`

Per vedere tutte le proprietà disponibili:

```powershell
Get-VM | Get-Member
```

## 6️Formattazione dell’output

PowerShell decide automaticamente se mostrare:

* Tabella
* Lista

Comandi utili:

* `Format-List`
* `Format-Table`

`Out-GridView` apre una finestra grafica con:

* Ricerca testuale
* Filtri dinamici

⚠ Disponibile solo su Windows.

---

## Where-Object (filtraggio avanzato)

Permette di filtrare risultati nella pipeline.

Struttura base:

```
Get-VM | Where-Object { $_.Proprietà -operatore valore }
```

* `$_` rappresenta l’oggetto corrente
* Si usa la notazione con il punto per accedere alle proprietà

Esempio:
Filtrare VM accese:

```powershell
$_.PowerState -eq "PoweredOn"
```

## Operatori di confronto

### Stringhe

* `-eq` → uguale
* `-ne` → diverso
* `-like` → confronto con wildcard
* `-notlike` → non corrisponde

### Numeri

* `-lt` → minore di
* `-le` → minore o uguale
* `-gt` → maggiore di
* `-ge` → maggiore o uguale


## Filtri multipli

Si possono combinare condizioni con operatori logici:

* `-and`
* `-or`

Esempio:
VM con almeno 2 CPU:

```powershell
$_.NumCPU -ge 2
```

Si possono usare più condizioni racchiudendole tra parentesi.

Ad esempio
```powershell
Get-VM | Where-Object -FilterScript {($_.PowerState -eq "PoweredOff") -and ($_.MemoryGB -ge 2)}
```


## Combinare Where-Object e Select-Object

Spesso si filtra prima e poi si selezionano solo alcune proprietà:

* Nome VM
* Memoria
* Data creazione

Se una proprietà sembra non esistere, usare:

```
Get-VM | Get-Member
```

per verificarne il nome corretto.


## Sort-Object

Serve a ordinare l’output.

Esempio:

```powershell
Sort-Object -Property CreateDate
```

Di default:

* Ordinamento crescente

Per decrescente:

```powershell
Sort-Object -Property CreateDate -Descending
```
