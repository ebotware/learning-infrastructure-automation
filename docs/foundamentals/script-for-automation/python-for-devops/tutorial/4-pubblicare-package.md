---
sidebar_position: 4
---

# Creare un package in Python

Questa guida spiega come è struttato un package e come creare uno usando solo gli strumenti standard Python (nessuna dipendenza esterna come `uv`, Poetry, Hatch o simili) utilizzando il formato moderno `pyproject.toml`.

L’esempio sarà estremamente semplice: un package chiamato `saluti` che contiene una sola funzione per stampare un saluto personalizzato.

## Struttura del package

Ecco come deve essere organizzata la cartella del progetto:

```
saluti/
├── pyproject.toml          # Configurazione principale del package
├── README.md               # Documentazione (obbligatoria per PyPI)
├── LICENSE                 # Licenza
├── src/
|    └── saluti/            # Il vero package Python
|       ├── __init__.py     # File per "avvisare" che la cartella è un package
|       └── funzioni.py     # Codice effettivo
└── dist/                   # Non è da creare manualmente, ma conterrà i file compilati del package

```

### Scopo di ogni file/cartella

| File / Cartella       | Scopo                                                                 |
|-----------------------|-----------------------------------------------------------------------|
| `pyproject.toml`      | File di configurazione. Dice a Python nome, versione, dipendenze e come buildare il package. È lo standard moderno. |
| `README.md`           | Descrizione del package. È quello che gli utenti leggono su PyPI o GitHub. |
| `LICENSE`             | Specifica i diritti di utilizzo (es. MIT, Apache 2.0). Senza licenza il package non dovrebbe essere distribuito. |
| `src/`                | Cartella che contiene il codice sorgente. Usare `src` è una **best practice** perché evita conflitti di nomi durante lo sviluppo. |
| `saluti/`             | La cartella del package. Deve avere lo **stesso nome** che verrà usato in `import saluti`. |
| `__init__.py`         | File (anche vuoto) che dice a Python “questa cartella è un package”. Può anche esportare funzioni per rendere l’import più pulito. |
| `funzioni.py`         | File con all'interno la logica effettiva del package.|

---

## Guida alla creazione di un package
In questa sezione verrà spiegato come creare un package

### 1. Creazione della struttura

```bash
mkdir saluti
cd saluti
mkdir -p src/saluti
```

### 2. Creazione dei file

#### `pyproject.toml`
```toml
[project]
name = "saluti"
version = "0.1.0"
description = "Un package Python per fare saluti"
readme = "README.md"
requires-python = ">=3.8"
license = {text = "MIT"}
dependencies = []

[build-system]
requires = ["setuptools >= 61.0"]
build-backend = "setuptools.build_meta"

[tool.setuptools.packages.find]
where = ["src"]
```

#### `README.md`
~~~markdown
# saluti

Un package Python di esempio che saluta le persone.

## Installazione

```bash
pip install .
```

## Utilizzo

```python
from saluti import saluta

print(saluta("Mondo"))
# → Ciao, Mondo!
```
~~~

#### `src/saluti/__init__.py`
```python
from .funzioni import saluta

__all__ = ["saluta"]
```

#### `src/saluti/funzioni.py`
```python
def saluta(nome: str) -> str:
    """Restituisce un saluto amichevole."""
    return f"Ciao, {nome}!"
```

#### `LICENSE` (opzionale ma consigliato)
Copia il testo della licenza MIT da [choosealicense.com](https://choosealicense.com/licenses/mit/).

### 3. Installare il package in modalità sviluppo

```bash
# Dalla cartella saluti/
python -m pip install -e .
```

Il flag `-e` (editable) fa si che pip installa il pacchetto collegandolo alla cartella del progetto, invece di copiarlo dentro site-packages.

Questo significa che ogni modifica al codice viene subito riflessa, senza dover reinstallare il pacchetto.

### 4. Test rapido in modalità interattiva

Aprire python in modalità interattiva Python ed eseguire:

```python
>>> from saluti import saluta
>>> saluta("Marco")
'Ciao, Marco!'
```

Se l'output è "Ciao, Marco!", allora il package è stato imporato in maniera corretta

### 5. Creazione distributabile

Per generare i file `.whl` e `.tar.gz` e distribuirli:

```bash
python -m pip install build
python -m build
```

Questi comandi genereranno la cartella `dist/` con i file pronti per essere installati o caricati su PyPI.


