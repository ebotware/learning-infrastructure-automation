---
sidebar_position: 3
---

# Moduli importanti per l'automazione Devops
In questo capitolo verranno descritti i moduli principali per interagire con il sistema operativo ed eseguire
azioni remote in SSH.


## Modulo `os` – interazione base col sistema operativo

Modulo built-in standard per permettere l'interazione con il sistema operativo

### A cosa serve

* Gestione file e directory
* Variabili d’ambiente
* Comandi di sistema (limitati)

Oggi è consigliato **evitare `os.system()`** per comandi complessi → meglio `subprocess`.

### Esempi comuni

```python
import os

# Directory corrente
print(os.getcwd())

# Creare directory
os.makedirs("backup", exist_ok=True)

# Elencare file
files = os.listdir("/var/log")
print(files)

# Variabili d'ambiente
token = os.getenv("API_TOKEN")
```

**Da usare per**: filesystem, path, env vars\
**Non ideale per**: eseguire comandi shell complessi

---

## `subprocess` – eseguire comandi di sistema (locali)

Il vero cavallo da battaglia per automazione locale

**A cosa serve**

* Eseguire comandi shell
* Catturare output e errori
* Controllo totale su input/output

### Esempio base

```python
import subprocess

result = subprocess.run(
    ["ls", "-l"],
    capture_output=True,
    text=True
)

print(result.stdout)
```

### Esempio con gestione errori

```python
result = subprocess.run(
    ["systemctl", "status", "nginx"],
    capture_output=True,
    text=True
)

if result.returncode != 0:
    print("Errore:", result.stderr)
else:
    print(result.stdout)
```


**Da usare per**:

* script di deploy
* monitoring
* gestione servizi
* cron automation

---

## `paramiko` – automazione **remota via SSH**

Libreria esterna per il controllo di server remoti

### A cosa serve

* Connessione SSH
* Esecuzione comandi remoti
* Upload/download file (SFTP)

### Connessione SSH + comando

```python
import paramiko

client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

client.connect(
    hostname="192.168.1.10",
    username="admin",
    password="password"
)

stdin, stdout, stderr = client.exec_command("uptime")
print(stdout.read().decode())

client.close()
```

### Upload file (SFTP)

```python
sftp = client.open_sftp()
sftp.put("local.txt", "/tmp/remote.txt")
sftp.close()
```

### Da usare per:

* fleet management
* deploy su più server
* task schedulati remoti
* backup automatizzati

## `fabric` - automazione **alto livello remota SSH**
Come `paramiko`, fabric è una libreria più ad alto livello per lavorare in **SSH**.

### Confronto codice
**Paramiko** (basso livello)

```py
import paramiko

client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect("host.esempio.it", username="utente", key_filename="~/.ssh/id_rsa")

stdin, stdout, stderr = client.exec_command("df -h && uptime")
print(stdout.read().decode())

client.close()
```
**Fabric 3** (alto livello - per la maggior parte degli usi)
```py
from fabric import Connection

c = Connection("utente@host.esempio.it", connect_kwargs={"key_filename": "~/.ssh/id_rsa"})

result = c.run("df -h && uptime", hide=True)
print(result.stdout)

# Esempio trasferimento file
c.put("file_locale.txt", "/percorso/remoto/")
c.get("/var/log/app.log", "backup.log")
```
### fabfile.py
È inoltre possibile definire un fabfile in modo da strutturare le fasi di deploy / status etc.

```py
# fabfile.py
from fabric import Connection, task

HOST = "your-server.example.com"
USER = "deploy"
KEY = "~/.ssh/id_ed25519"
APP_DIR = "/opt/myapp"
VENV = f"{APP_DIR}/venv"

def conn():
    return Connection(
        host=HOST,
        user=USER,
        connect_kwargs={"key_filename": KEY}
    )

@task
def deploy(c, restart=True):
    """Deploy semplice: aggiorna codice e riavvia"""
    c = conn()
    
    with c.cd(APP_DIR):
        # Aggiorna codice
        c.run("git pull")
        
        # Aggiorna dipendenze
        with c.prefix(f"source {VENV}/bin/activate"):
            c.run("pip install -r requirements.txt")
        
        # Riavvia (cambia il nome del servizio!)
        if restart:
            c.sudo("systemctl restart myapp")

    print("Deploy finito!")

@task
def status(c):
    """Vede lo stato del servizio"""
    c = conn()
    c.sudo("systemctl status myapp --no-pager -l")

@task
def logs(c, n=30):
    """Ultimi log"""
    c = conn()
    c.sudo(f"journalctl -u myapp -n {n} --no-pager")
```
Esecuzione:
```py
# Deploy normale
fab deploy

# Deploy senza riavviare
fab deploy --no-restart

# Vedere lo stato
fab status

# Ultimi 50 log
fab logs --n 50
```


## Platform
È un modulo standard Python per ottenere informazioni sul sistema e sull’ambiente di runtime. Ad esempio:
```py
import platform

print(platform.system())     # Linux / Windows / Darwin
print(platform.release())    # Versione kernel
print(platform.version())    # Dettagli kernel
print(platform.machine())    # x86_64, arm64
print(platform.processor())  # CPU
```
Esempio output
```py
Linux
5.15.167.4-microsoft-standard-WSL2
#1 SMP Tue Nov 5 00:21:55 UTC 2024
x86_64
```

Zero dipendenze, perfetto per script portabili.

## Logging
Logging è un modulo standard python. Mette a disposizione le seguenti features:
* livelli (DEBUG / INFO / WARNING / ERROR / CRITICAL)
* output su **file**, **console**, **syslog**, **CloudWatch**
* timestamp, moduli, PID, thread

### Livelli di log
Tabella recap dei livelli di log

| Livello  | Quando usarlo                       |
| -------- | ----------------------------------- |
| DEBUG    | dettagli tecnici                    |
| INFO     | flusso normale                      |
| WARNING  | situazione anomala ma non bloccante |
| ERROR    | operazione fallita                  |
| CRITICAL | sistema inutilizzabile              |

## `sys` - accesso informazioni runtime Python
Modulo che dà accesso al runtime Python stesso.

Usato principlamente per:
* Exit Code
* Info runtime Python
* Stdout/stderr

### Exit Code

```py
sys.exit(0)   # OK
sys.exit(1)   # Errore generico
sys.exit(2)   # Uso errato
```
### Info runtime Python
```py
if sys.version_info < (3, 10):
    sys.exit("Python >= 3.10 richiesto")
```

### Stdout/stderr
```py
>>> sys.stdout.write("Hi\n")
Hi # <- Carattere stampato
3  # <- Ritorno di write (caratteri stampati)
```

## requests + urllib3
* urllib3 → motore HTTP low-level
* requests → API high-level costruita sopra urllib3

requests usa urllib3 sotto il cofano.

### urllib3

Esempio:

```py
import urllib3

http = urllib3.PoolManager()

r = http.request("GET", "https://httpbin.org/get")

print(r.status)
print(r.data)
```

### requests
Esempio:

```py
import requests

r = requests.get("https://httpbin.org/get")
print(r.status_code)
print(r.json())
```
Gestisce JSON, headers, cookies automaticamente

## JSON
Modulo standard
```py
import json
# Fa file
with open("config.json") as f:
    data = json.load(f)
# Da stringa testo
data = json.loads('{"name": "Mario", "age": 30}')
# Scrivere JSON
with open("out.json", "w") as f:
    json.dump(data, f, indent=2)
```

## YAML
Installazione
```py
pip install pyyaml
```
Caricare YAML
```py
import yaml

with open("config.yaml") as f:
    data = yaml.safe_load(f)
```

## CSV

```py
import csv
```

```py
with open("users.csv", newline="") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["email"])
```

```py
email,role,active
a@x.com,admin,true
```

```py
with open("out.csv", "w", newline="") as f:
    writer = csv.DictWriter(
        f,
        fieldnames=["email", "role"]
    )
    writer.writeheader()
    writer.writerow({"email": "a@x.com", "role": "admin"})
```

## Bibliografia e fonti

Lista tratta da [https://devopscube.com/python-for-devops/#important-python-modules-for-devops-automation](https://devopscube.com/python-for-devops/#important-python-modules-for-devops-automation)
