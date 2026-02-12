# PIP
In questo capitolo verrà trattato il packet manager più popolare di Python, `pip`.

## Terminologie
Prima di affrontare `pip` nel dettaglio, in questo capitolo verranno discusse le terminologie più comuni

### Moduli (Modules)
A differenza degli **script**, i **modules** sono file python scritti con l'intento di essere richiamati.

Python **Module** `greeter.py`
```py
def sayHi():
    return "Hi!"
```

Python **Script**
```py
def sayHi():
    return "Hi!"
sayHi()
```

Python **Script** che richiama il modulo `greeter.py`
```py
from greeter import sayHi
sayHi()
```

### Packages
I packages sono una collezione di **Modules**. Sono normalmente un insieme di file `.py` raggruppati in una cartella.

Esiste una collezione di packages già disponibile di default con l'installazione di Python. Questi Packages sono parte della [Python standard library](https://docs.python.org/3/library/index.html) e **non devono essere installati con pip**.

## Cos'è un package manager?
Un package manager è un tool che **aiuta a gestire le dipendenze di un progetto**. In Python, una dipendenza è una porzione di codice richiesta al programma per funzionare correttamente. In Python, questi vengono forniti la maggior parte delle volte sottoforma di **packages**.


## Virtual environments
Un container isolato con all'interno un interprete Python, un eseguibili pip e una directory con dentro i packages installati

È una pratica comune avere uno (o più) ambienti virtuali dedicati per ogni progetto Python.

https://realpython.com/courses/working-python-virtual-environments/

### Creare un virtual environment
*   **Windows** - Per creare ed attivare un venv su **Windows** eseguire e seguenti comandi (testato con **Powershell**)
    ```ps
    python -m venv .\.myenv
    .\.myenv\Scripts\activate
    ```
## Introduzione a PIP

Il Package manager di Python si chiama `pip`, ed è preinstallato nelle versioni più recenti di Python.

Di default `pip` cerca i pacchetti da installare su [PyPI](https://pypi.org/), un repository pubblico che contiene centinaia di pacchetti scritti dalla community Python.

### Installare un package con PIP
Per installare un package con `pip` digitare:
```sh
pip install <nome del package>
# Ad esempio
pip install requests
```
Per visualizzare il risultato eseguire
```sh
pip list
```

## Requirement Files
Un file requirement (**requirements file**) contiene una lista di tutte le dipendenze del progetto. 

Il file `requirements.txt` viene generato sfruttando il comando `pip freeze` che elenca tutta le dipendenze di un progetto nello `stdout`. 

```sh
pip freeze > requirements.txt
```

Questo genererà il file `requirements.txt`, dal quale sarà possibile installare le dipendenze del progetto eseguendo:

```sh
pip install -r requirements.txt
```

Significato parametro `-r` ([pip.pypa.io](https://pip.pypa.io/en/latest/cli/pip_install/#options))
```
-r, --requirement <file>
    Install from the given requirements file. This option can be used multiple times.

    (environment variable: PIP_REQUIREMENT)
```

### Aggiornare i packages in base a requirements.txt
Per aggiornare i packages in `requirements.txt` eseguire:

```py
pip install --upgrade -r requirements.txt
```

Questo comando va generalmente eseguito dopo aver modificato nel file requirements un entry da:
```
requests==2.32.5
```
a 
```
requests>=2.32.5
```
In modo da segnalare che per questo specifico package abbiamo bisogno della **versione 2.32.5 o superiore**

### Dipendenze, Production vs Development
È un'esigenza comune che i pacchetti di sviluppo non debbano essere installati quando si deve fare una build in produzione.

Un esempio sono i pacchetti di test come `pytest`. Includerli in produzione appesantirebbe unicamente il prodotto finale

Per evitare questo inconveniente, viene generalmente creato un file dedicato per definire le dipendenze in test `requirements_dev.txt`.

Il file `requirements_dev.txt` conterrà quindi le dipendenze del programma base e le dipdenze di test.

```
-r requirements.txt
pytest==9.0.2
```

### Lockfile
È anche utile creare un lockfile, con tutte le dipendenze (anche transitivi) bloccate. Così da avere una versione che sappiamo funziona con le dipendenze specificaten nel nostro applicativo. 

```
cp requirements.txt requirements_locked.txt
# Bloccare le dipdendenze cambiando >= in ==
```