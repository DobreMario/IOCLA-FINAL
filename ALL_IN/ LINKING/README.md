# README pentru Makefile-uri ASM/C

Acest director conține exemple de Makefile-uri pentru linking între fișiere ASM și C, atât pentru linking static cât și dinamic.

---

**1. Makefile_ASM**
- Linking static între două fișiere ASM (main.asm și functie.asm).
- Comenzi principale: `nasm` pentru asamblare, `ld` pentru linking static.
- Rulează: `make -f Makefile_ASM`

**2. Makefile_c_ASM**
- Linking static între C și ASM (main.c apelează asm_func.asm).
- Comenzi principale: `gcc` pentru C, `nasm` pentru ASM.
- Rulează: `make -f Makefile_c_ASM`

**3. Makefile_c_multi_asm**
- Linking static între mai multe fișiere C și ASM (main.c, utils.c, asm_func.asm).
- Comenzi principale: `gcc`, `nasm`.
- Rulează: `make -f Makefile_c_multi_asm`

**4. Makefile_c_only**
- Linking static doar între fișiere C (main.c, utils.c).
- Comenzi principale: `gcc`.
- Rulează: `make -f Makefile_c_only`

---

## Linking Static vs. Linking Dinamic

- **Linking static:** toate fișierele obiect și bibliotecile sunt incluse în executabil la build. Executabilul nu depinde de biblioteci externe la rulare.
  - Exemplu: `gcc main.o -o program -static` sau fără opțiuni suplimentare pentru biblioteci statice locale.
- **Linking dinamic:** executabilul depinde de biblioteci partajate (.so pe Linux) care sunt încărcate la rulare. Executabilul este mai mic, dar are dependențe la runtime.
  - Exemplu: `gcc main.o -o program -lm` (leagă cu libm.so)

### Makefile pentru linking dinamic cu o bibliotecă partajată

```makefile
# Makefile_dynamic
all: program

program: main.o
	gcc main.o -o program -lm # linking dinamic cu libm.so (math)

main.o: main.c
	gcc -c main.c

clean:
	rm -f *.o program
```

### Makefile pentru linking static cu o bibliotecă statică

```makefile
# Makefile_static
all: program

program: main.o libfoo.a
	gcc main.o -o program -static -L. -lfoo # linking static cu libfoo.a

main.o: main.c
	gcc -c main.c

libfoo.a: foo.o
	ar rcs libfoo.a foo.o

foo.o: foo.c
	gcc -c foo.c

clean:
	rm -f *.o *.a program
```

---

**Despre simbolurile speciale în Makefile:**
- `$@` = numele targetului curent (ex: program, main.o)
- `$^` = lista tuturor dependențelor (ex: main.o functie.o)
- `$<` = prima dependență (de obicei folosită la reguli de compilare, ex: sursa pentru un .o)
- `$*` = numele targetului fără extensie (ex: pentru main.o → main)
- `$?` = lista dependențelor care sunt mai noi decât targetul (utile pentru build incremental)

**Exemple de utilizare:**
- `$(CC) -c $< -o $@` — compilează primul fișier sursă în targetul .o
- `echo $*` — afișează numele fără extensie

Aceste simboluri ajută la scrierea de reguli generice și automate în Makefile-uri complexe.

---

Fiecare Makefile are și o regulă `clean` pentru ștergerea fișierelor generate.
