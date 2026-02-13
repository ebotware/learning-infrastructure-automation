---
sidebar_position: 5
---

# CI/CD con Python
In questo capitolo verranno trattati i concetti chiavi della Continuos Integration, in particolare come eseguire test, installare dipendenze e creare 
virtual environments.

## Creare un programma base da testare

```py
"""
calculator.py
Calculator library containing basic math operations.
"""

def add(first_term, second_term):
    return first_term + second_term

def subtract(first_term, second_term):
    return first_term - second_term
```

## Linting
Il package più famoso per il linting è `flake8`. Serve a controllare automaticamente se il codice rispetta le 
convenzioni di stile e a individuare errori comuni o potenziali problemi. Per installarlo eseguire

```shell
python -m pip install flake8
```

Per eseguire `flake8` digitare

```shell
flake8
```

L'output di flake8 ci segnalerà gli errori di stile riguardo l'esempio riportato:

```sh
.\calculator.py:6:1: E302 expected 2 blank lines, found 1
.\calculator.py:9:1: E302 expected 2 blank lines, found 1
.\calculator.py:10:36: W292 no newline at end of file
```

Aspettandosi il risultato aspettato ottimale seguente (notare le nuove righe):

```py
"""
calculator.py
Calculator library containing basic math operations.
"""


def add(first_term, second_term):
    return first_term + second_term


def subtract(first_term, second_term):
    return first_term - second_term

```

## Testing
Per testare l'applicazione occorre innanzitutto innanzitutto installare il framework di test.

```shell
pip install pytest
```

Tuttavia se adesso facissimo eseguire i test, otterremmo un messaggio simile:

```shell
========================== no tests ran in 0.01s ==========================
```

Notificandoci che non è stato creato ancora nessun file di test.

Per il nostro esempio, chiameremo questo file `test_calculator.py`. Da notare il prefisso `test_`, che serve ad indicare al framework `pytest` che il file contiene dei test. 

All'interno del file inseriamo il seguente contenuto:

```py
import calculator

class TestCalculator:
    def test_addition(self):
        assert 4 == calculator.add(2, 2)

    def test_subtraction(self):
        assert 2 == calculator.subtract(4, 2)
```

Questa volta, eseguire pytest dovrebbe ritornare un output simile:

```
collected 2 items                                                          

test_calculator.py ..                                                [100%] 

============================ 2 passed in 0.02s ============================
```