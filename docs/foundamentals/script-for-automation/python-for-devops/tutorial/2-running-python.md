---
sidebar_position: 2
---

# Esegure Codice Python
Descrizione dei diversi modi in cui è possibile eseguire codice Python

## Esegure Codice Python Iterativamente (Interactively)
In Linux, per eseguire Python da terminale digitare `python3`. Ad esempio:
```py
>>> print("Hello World!")
Hello World!
>>> 2 + 5
7
>>> a = "Hi!"
>>> print(a)
Hi!
>>> exit()
```

## Eseguire Python dalla linea di comando
In Linux, creare un file .py
```py
touch hello.py
```

Modificare il contenuto come segue
```py
print("Hello world")
```

Eseguire lo script
```py
python3 hello.py"
```

L'output del comando dovrebbe essere
```
Hello World
```

## Eseguire Python dal file manager
Su **Windows**, **Linux** e *MacOS** è possibile eseguire i file direttamente dal filemanager. 

Tuttavia se viene cliccato lo script da eseguire, una finestra a terminale comparirà per pochi istanti senza dare il tempo all'utente di capire cosa è stato eseguito.

Per questo motivo, è consigliabile aggiungere alla fine dello script il seguente comando per dare all'untente il tempo di leggere prima di chiudere la finestra.
```py
input("Press Enter to continue...")
```
### Eseguire file py da file manager su Linux-Ubuntu-20.04
Per consentire l'esecuzione dei file .py su Ubuntu da filemanager eseguire i seguenti passaggi:
1. Rendere il file eseguibile `chmod +x <nome del file>`
2. In Ubuntu, spuntare nelle opzioni del filemanager in **Executable Text Files** → **Ask what to do**
3. Aggiungere la seguente linea di codice **ad inizio file**
    ```py
    #!/usr/bin/env python3
    ```