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

Esiste una collezione di packages già disponibile di default con l'installazione di Python. Questi Packages sono parte della [Python standard](https://docs.python.org/3/library/index.html) library e **non devono essere installati con pip**.


