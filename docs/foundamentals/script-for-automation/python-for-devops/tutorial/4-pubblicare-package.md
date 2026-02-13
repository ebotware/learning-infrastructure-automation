---
sidebar_position: 4
---

# Creare e pubblicare un package [WIP]

Per creare e pubblicare un package occorre:

1. Strutturare correttamente il progetto
2. Preparare i file di configurazione
3. Costruire (Build) il pacchetto
4. Pubblicarlo su PyPI

## Struttura base del progetto

```
my_package/
│
├── src/
│   └── my_package/
│       ├── __init__.py
│       └── core.py
│
├── tests/
│   └── test_core.py
│
├── README.md
├── LICENSE
├── pyproject.toml
└── .gitignore
```

