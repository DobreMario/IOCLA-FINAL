# 🧠 IOCLA Resources

Acest repository conține materiale structurate pentru învățarea și aprofundarea conceptelor predate în cadrul cursului **IOCLA (Introducerea în Organizarea Calculatoarelor și Limbaj de Asamblare)**.

## 📁 Structura repository-ului

```
ALL_IN/
├── README.md           # Informații generale despre partea de asamblare
├── LINKING/            # Detalii despre procesul de linking + exemple Makefile
│   ├── README.md
│   ├── Makefile_ASM
│   ├── Makefile_c_ASM
│   ├── Makefile_c_multi_asm
│   ├── Makefile_c_only
│   ├── Makefile_dynamic
│   └── Makefile_static
├── C_MEMORY/           # Declarații de variabile în C în diferite zone de memorie
│   └── README.md       # Explicații despre variabile exportabile și neexportabile
```

## 📚 Conținut

### Asamblare (`ALL_IN/README.md`)
Prezentare generală a noțiunilor fundamentale de limbaj de asamblare, inclusiv instrucțiuni uzuale, organizarea codului și moduri de adresare.

### Linking (`ALL_IN/LINKING/`)
Explicarea procesului de linking, diferența dintre linking static și dinamic, simboluri externe și interne, plus exemple funcționale de Makefile pentru compilare și legare (inclusiv linking cu biblioteci statice și dinamice).

### Memorie în C (`ALL_IN/C_MEMORY/`)
Demonstrări concrete ale modului în care variabilele pot fi declarate în segmente de memorie diferite (stack, heap, `.data`, `.bss`, `.rodata`, TLS) și dacă sunt sau nu exportabile între fișiere (`extern`, `static`, `global` etc).
