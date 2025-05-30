# Introducere
Acest document conține informații despre programarea în limbaj de asamblare (ASM) pe arhitectura x86, incluzând registre, instrucțiuni, directive, structuri, vectori, stivă și funcții.

---

# 1. Registre în ASM

## 1.1 Registre Generale
- **EAX**: Registru acumulator utilizat pentru operații aritmetice și logice.
- **EBX**: Registru de bază utilizat pentru accesarea datelor.
- **ECX**: Registru contor utilizat în bucle și operații de repetiție.
- **EDX**: Registru de date utilizat pentru operații de intrare/ieșire și multiplicare/divizare.

## 1.2 Registre Index
- **ESI**: Registru sursă utilizat pentru operații de manipulare a memoriei (ex. `movs`, `cmps`).
- **EDI**: Registru destinație utilizat pentru operații de manipulare a memoriei.

## 1.3 Registre de Stivă
- **ESP**: Registru pointer pentru stivă, indică vârful stivei.
- **EBP**: Registru de bază pentru stivă, utilizat pentru referințe la variabile locale.

## 1.4 Registre de Segment
- **CS**: Segmentul de cod, indică locația codului executabil.
- **DS**: Segmentul de date, indică locația datelor.
- **SS**: Segmentul de stivă, indică locația stivei.
- **ES, FS, GS**: Registre suplimentare pentru segmente, utilizate pentru acces specializat la memorie.

## 1.5 Registre de Control
- **EIP**: Pointerul de instrucțiuni, indică adresa următoarei instrucțiuni de executat.
- **EFLAGS**: Registru de stare, conține indicatori pentru rezultatele operațiilor (ex. zero, carry, overflow).

## 1.6 Note Suplimentare
- Registrele pe 32 de biți (ex. `EAX`) sunt extensii ale registrelor pe 16 biți (ex. `AX`).
- Registrele pot fi accesate parțial:
  - `AX` (16 biți), `AH` (8 biți superiori), `AL` (8 biți inferiori) pentru `EAX`.
- Arhitectura x86 utilizează registrele pentru operații de nivel jos, inclusiv manipularea memoriei, controlul fluxului și operații aritmetice.

---

# 2. Instrucțiuni Utile în ASM

## 2.1 Instrucțiuni de Bază
- **mov dst, src**: Mută valoarea din sursă în destinație (înlocuind ce era anterior în destinație).
  - Exemplu:
    ```asm
    mov eax, ebx ; Copiază valoarea din EBX în EAX
    ```

- **push src**: Mută valoarea din sursă pe "vârful" stivei.
  - Exemplu:
    ```asm
    push eax ; Salvează valoarea din EAX pe stivă
    ```

- **pop dst**: Mută valoarea de pe "vârful" stivei în destinație.
  - Exemplu:
    ```asm
    pop ebx ; Restaurează valoarea de pe stivă în EBX
    ```

- **lea dst, src**: Încarcă adresa efectivă a sursei în destinație.
  - Exemplu:
    ```asm
    lea eax, [ebx+4] ; Încarcă adresa EBX+4 în EAX
    ```

- **xchg dst, src**: Schimbă valorile între sursă și destinație.
  - Exemplu:
    ```asm
    xchg eax, ebx ; Schimbă valorile dintre EAX și EBX
    ```

## 2.2 Instrucțiuni Aritmetice și Logice
- **add dst, src**: Adună valoarea sursei la destinație, rezultatul fiind stocat în destinație.
  - Exemplu:
    ```asm
    add eax, ebx ; Adaugă valoarea din EBX la EAX
    ```

- **sub dst, src**: Scade valoarea sursei din destinație, rezultatul fiind stocat în destinație.
  - Exemplu:
    ```asm
    sub eax, 5 ; Scade 5 din valoarea din EAX
    ```

- **and dst, src**: Calculează AND logic între sursă și destinație, rezultatul fiind stocat în destinație.
  - Exemplu:
    ```asm
    and eax, 0xFF ; Păstrează doar ultimii 8 biți din EAX
    ```

- **or dst, src**: Calculează OR logic între sursă și destinație, rezultatul fiind stocat în destinație.
  - Exemplu:
    ```asm
    or eax, ebx ; Face OR logic între EAX și EBX
    ```

- **xor dst, src**: Calculează XOR logic între sursă și destinație, rezultatul fiind stocat în destinație.
  - Exemplu:
    ```asm
    xor eax, eax ; Resetează EAX la 0
    ```

- **test dst, src**: Calculează AND logic între sursă și destinație fără a stoca rezultatul, doar setează flag-urile.
  - Exemplu:
    ```asm
    test eax, eax ; Verifică dacă EAX este 0
    ```

## 2.3 Instrucțiuni de Shift
- **shl dst, <const>**: Realizează un shift logic la stânga al valorii din destinație cu un număr constant de poziții, rezultatul fiind stocat în destinație.
  - Exemplu:
    ```asm
    shl eax, 1 ; Mută biții din EAX la stânga cu 1 poziție
    ```

- **shr dst, <const>**: Realizează un shift logic la dreapta al valorii din destinație cu un număr constant de poziții, rezultatul fiind stocat în destinație.
  - Exemplu:
    ```asm
    shr eax, 1 ; Mută biții din EAX la dreapta cu 1 poziție
    ```

## 2.4 Instrucțiuni de Multiplicare și Împărțire
- **mul src**: Realizează o multiplicare fără semn între valoarea din sursă și valoarea implicită din `EAX` (sau `AX`/`AL` în funcție de dimensiune). Rezultatul este stocat în `EDX:EAX` (sau `DX:AX`, `AH:AL`).
  - Pentru **mulțirea pe 8 biți** (`mul bl`):
    - `AL * bl` → rezultatul (16 biți) este stocat în `AX` (`AH:AL`).
    - Exemplu:
      ```asm
      mov al, 5
      mov bl, 10
      mul bl ; AX = AL * BL (rezultat pe 16 biți)
      ```
  - Pentru **mulțirea pe 16 biți** (`mul bx`):
    - `AX * bx` → rezultatul (32 biți) este stocat în `DX:AX`.
    - Exemplu:
      ```asm
      mov ax, 1000
      mov bx, 3
      mul bx ; DX:AX = AX * BX (rezultat pe 32 biți)
      ```
  - Pentru **mulțirea pe 32 biți** (`mul ebx`):
    - `EAX * ebx` → rezultatul (64 biți) este stocat în `EDX:EAX`.
    - Exemplu:
      ```asm
      mov eax, 5
      mov ebx, 10
      mul ebx ; EDX:EAX = EAX * EBX (rezultat pe 64 biți)
      ```
  - **Atenție:** Dacă rezultatul nu încape în registrul de bază (ex: AL, AX, EAX), partea superioară (`AH`, `DX`, `EDX`) va fi diferită de zero.

- **div src**: Realizează o împărțire fără semn între valoarea din `EDX:EAX` (sau `DX:AX`, `AH:AL`) și valoarea din sursă. Rezultatul este stocat în registrul de bază (câtul) și registrul superior (restul).
  - Pentru **împărțirea pe 8 biți** (`div bl`):
    - `AX / bl` → câtul în `AL`, restul în `AH`.
    - Exemplu:
      ```asm
      mov ax, 100
      mov bl, 7
      div bl ; AL = AX / BL (cât), AH = AX % BL (rest)
      ```
  - Pentru **împărțirea pe 16 biți** (`div bx`):
    - `DX:AX / bx` → câtul în `AX`, restul în `DX`.
    - Exemplu:
      ```asm
      mov dx, 0
      mov ax, 1000
      mov bx, 7
      div bx ; AX = (DX:AX) / BX (cât), DX = rest
      ```
  - Pentru **împărțirea pe 32 biți** (`div ebx`):
    - `EDX:EAX / ebx` → câtul în `EAX`, restul în `EDX`.
    - Exemplu:
      ```asm
      mov edx, 0
      mov eax, 1000
      mov ebx, 7
      div ebx ; EAX = (EDX:EAX) / EBX (cât), EDX = rest
      ```
  - **Atenție:** Înainte de împărțire, registrul superior (`AH`, `DX`, `EDX`) trebuie să fie 0 pentru a evita rezultate neașteptate.

### Note suplimentare
- Pentru **mul** și **div**, dimensiunea operandului determină registrele implicate și dimensiunea rezultatului.
- Pentru operații cu semn, se folosesc instrucțiunile `imul` (înmulțire cu semn) și `idiv` (împărțire cu semn), cu aceleași reguli de registre.
- Dacă rezultatul nu încape în registrul de bază, se setează flag-ul de overflow.
- Împărțirea la zero produce excepție (crash).

## 2.5 Instrucțiuni de Salt (Jump)
Instrucțiunile de salt sunt utilizate pentru a controla fluxul execuției în funcție de condiții sau necondiționat.

### Salturi Necondiționate
- **jmp label**: Sare la eticheta specificată.
  - Exemplu:
    ```asm
    jmp loop_start ; Sare la eticheta `loop_start`
    ```

### Salturi Condiționate
Salturile condiționate depind de flag-urile setate în registrul `EFLAGS`.

- **je label** / **jz label**: Sare dacă rezultatul anterior este zero (equal).
- **jne label** / **jnz label**: Sare dacă rezultatul anterior nu este zero (not equal).
- **jg label** / **jnle label**: Sare dacă este mai mare (greater, semnat).
- **jge label** / **jnl label**: Sare dacă este mai mare sau egal (greater or equal, semnat).
- **jl label** / **jnge label**: Sare dacă este mai mic (less, semnat).
- **jle label** / **jng label**: Sare dacă este mai mic sau egal (less or equal, semnat).
- **ja label** / **jnbe label**: Sare dacă este mai mare (above, nesemnat).
- **jae label** / **jnb label**: Sare dacă este mai mare sau egal (above or equal, nesemnat).
- **jb label** / **jnae label**: Sare dacă este mai mic (below, nesemnat).
- **jbe label** / **jna label**: Sare dacă este mai mic sau egal (below or equal, nesemnat).

### Salturi pe Bază de Flag-uri
- **jc label**: Sare dacă flag-ul de carry (CF) este setat.
- **jnc label**: Sare dacă flag-ul de carry (CF) nu este setat.
- **jo label**: Sare dacă flag-ul de overflow (OF) este setat.
- **jno label**: Sare dacă flag-ul de overflow (OF) nu este setat.
- **js label**: Sare dacă flag-ul de semn (SF) este setat.
- **jns label**: Sare dacă flag-ul de semn (SF) nu este setat.
- **jp label** / **jpe label**: Sare dacă flag-ul de paritate (PF) este setat.
- **jnp label** / **jpo label**: Sare dacă flag-ul de paritate (PF) nu este setat.

### Salturi pe Bază de ECX
- **loop label**: Decrementează `ECX` și sare la eticheta specificată dacă `ECX` nu este zero.
  - Exemplu:
    ```asm
    loop loop_start ; Repetă bucla până când ECX devine 0
    ```

- **loope label** / **loopz label**: Decrementează `ECX` și sare dacă `ECX` nu este zero și rezultatul anterior este zero.
- **loopne label** / **loopnz label**: Decrementează `ECX` și sare dacă `ECX` nu este zero și rezultatul anterior nu este zero.

## Note Suplimentare
- Instrucțiunile de salt condiționat sunt utilizate împreună cu instrucțiuni precum `cmp` sau `test` pentru a seta flag-urile necesare.
- Salturile pe bază de `ECX` sunt utile pentru implementarea buclelor controlate de contor.

---

# 3. Directive de Definire a Datelor

## 3.1 Directive Comune
- **db (Define Byte)**: Rezervă 1 octet (8 biți) în memorie.
  - Exemplu:
    ```asm
    my_byte db 0x1A ; Definește un octet cu valoarea 0x1A
    ```
  - Dimensiune: 1 octet (8 biți).

- **dw (Define Word)**: Rezervă 2 octeți (16 biți) în memorie.
  - Exemplu:
    ```asm
    my_word dw 0x1234 ; Definește un cuvânt cu valoarea 0x1234
    ```
  - Dimensiune: 2 octeți (16 biți).

- **dd (Define Double Word)**: Rezervă 4 octeți (32 biți) în memorie.
  - Exemplu:
    ```asm
    my_dword dd 0x12345678 ; Definește un dublu cuvânt cu valoarea 0x12345678
    ```
  - Dimensiune: 4 octeți (32 biți).

## 3.2 Utilizare Avansată
- Se pot defini mai multe valori simultan:
  ```asm
  my_array db 1, 2, 3, 4 ; Definește un tablou de 4 octeți
  ```

- Se pot rezerva spații fără inițializare:
  ```asm
  buffer db 64 dup(?) ; Rezervă 64 de octeți neinițializați
  ```

- Se pot utiliza directive precum `times` pentru a inițializa un vector cu valori repetitive:
  ```asm
  my_vect times 100 dw 42 ; Creează un vector de 100 de elemente, fiecare având valoarea 42
  ```

## 3.3 Note Suplimentare
- Directivele sunt utilizate pentru a aloca și inițializa date în secțiunea `.data` sau `.bss` a unui program ASM.
- `dup` este util pentru a crea blocuri repetitive de date.

---

# 4. Structuri în ASM

## 4.1 Declarația unei Structuri
Structurile sunt definite utilizând directive precum `struc` și `res` pentru a rezerva memorie.

### Exemplu de Declarație
```asm
struc mystruct
    a: resw 1    ; a este un element de dimensiune word (2 octeți)
    b: resd 1    ; b este un element de dimensiune double-word (4 octeți)
    c: resb 1    ; c este un element de dimensiune byte (1 octet)
    d: resd 1    ; d este un element de dimensiune double-word (4 octeți)
    e: resb 6    ; e este un tablou de 6 elemente de dimensiune byte (6 octeți)
endstruc
```

### Explicație
- **`resw`**: Rezervă un cuvânt (2 octeți) pentru fiecare element.
- **`resd`**: Rezervă un dublu cuvânt (4 octeți) pentru fiecare element.
- **`resb`**: Rezervă un octet (1 octet) pentru fiecare element.

## 4.2 Inițializarea unei Structuri
Structurile pot fi inițializate utilizând directivele `istruc`, `at`, și `iend`.

### Exemplu de Inițializare
```asm
struct_var:
istruc mystruct
    at a, dw -1              ; Inițializează `a` cu -1
    at b, dd 0x12345678      ; Inițializează `b` cu 0x12345678
    at c, db ' '             ; Inițializează `c` cu un spațiu
    at d, dd 23              ; Inițializează `d` cu 23
    at e, db 'Gary', 0       ; Inițializează `e` cu "Gary" urmat de un terminator NULL
iend
```

### Explicație
- **`istruc`**: Începe inițializarea unei structuri.
- **`at`**: Specifică elementul din structură care trebuie inițializat.
- **`iend`**: Încheie inițializarea structurii.

## 4.3 Accesarea Elementelor din Structură
Accesarea elementelor dintr-o structură se face utilizând offset-uri calculate pe baza structurii.

### Exemplu de Accesare
```asm
mov eax, [struct_var + mystruct.a] ; Accesează elementul `a` din structură
mov ebx, [struct_var + mystruct.b] ; Accesează elementul `b` din structură
mov cl, [struct_var + mystruct.c]  ; Accesează elementul `c` din structură
```

### Explicație
- **`struct_var`**: Adresa de bază a structurii.
- **`mystruct.a`**: Offset-ul elementului `a` în structură.
- **`+`**: Adaugă offset-ul la adresa de bază pentru a accesa elementul.

## 4.4 Note Suplimentare
- Structurile sunt **packed** (aliniate compact) în ASM, ceea ce înseamnă că elementele sunt plasate consecutiv în memorie fără padding.
- Este important să cunoașteți dimensiunile fiecărui element pentru a calcula corect offset-urile.
- Directivele `resb`, `resw`, și `resd` sunt utilizate pentru a rezerva memorie pentru fiecare element din structură.

---

# 5. Vectori în ASM

## 5.1 Declararea Vectorilor

### În Secțiunea `.data`
Vectorii declarați în `.data` sunt inițializați la valori specifice în momentul compilării.
- Exemplu:
  ```asm
  section .data
  my_array db 1, 2, 3, 4, 5 ; Vector de 5 elemente de 1 octet (8 biți)
  my_array2 dw 10, 20, 30   ; Vector de 3 elemente de 2 octeți (16 biți)
  my_array3 dd 100, 200     ; Vector de 2 elemente de 4 octeți (32 biți)
  ```

### În Secțiunea `.bss`
Vectorii declarați în `.bss` sunt rezervați în memorie, dar nu sunt inițializați.
- Exemplu:
  ```asm
  section .bss
  uninit_array resb 10  ; Vector de 10 elemente de 1 octet (8 biți)
  uninit_array2 resw 5  ; Vector de 5 elemente de 2 octeți (16 biți)
  uninit_array3 resd 3  ; Vector de 3 elemente de 4 octeți (32 biți)
  ```

## 5.2 Accesarea Elementelor din Vectori
Accesarea unui element dintr-un vector se face utilizând adresa de bază a vectorului și un offset calculat pe baza dimensiunii elementelor.

### Exemplu de Accesare
- Pentru un vector de octeți:
  ```asm
  mov al, [my_array + 2] ; Accesează al treilea element (index 2) din `my_array`
  ```

- Pentru un vector de cuvinte:
  ```asm
  mov ax, [my_array2 + 2*2] ; Accesează al treilea element (index 2) din `my_array2`
  ```

- Pentru un vector de dublu cuvinte:
  ```asm
  mov eax, [my_array3 + 4*1] ; Accesează al doilea element (index 1) din `my_array3`
  ```

### Explicație
- **`my_array`**: Adresa de bază a vectorului.
- **`+`**: Adaugă offset-ul calculat.
- **`dimensiune_element * index`**: Offset-ul este calculat ca dimensiunea fiecărui element înmulțită cu indexul dorit.

## 5.3 Note Suplimentare
- Vectorii din `.data` sunt inițializați explicit, în timp ce cei din `.bss` sunt inițializați implicit cu zero (în unele cazuri, depinde de sistem).
- Directivele `db`, `dw`, și `dd` sunt utilizate pentru a defini vectori inițializați, iar directivele `resb`, `resw`, și `resd` sunt utilizate pentru a rezerva spațiu pentru vectori neinițializați.
- Accesarea elementelor din vectori necesită cunoașterea dimensiunii fiecărui element pentru a calcula corect offset-ul.

---

# 6. Stiva în ASM

## 6.1 Operații de Bază pe Stivă
- **push src**: Salvează valoarea din sursă pe vârful stivei.
  - Exemplu:
    ```asm
    push eax ; Salvează valoarea din EAX pe stivă
    ```

- **pop dst**: Restaurează valoarea de pe vârful stivei în destinație.
  - Exemplu:
    ```asm
    pop ebx ; Restaurează valoarea de pe stivă în EBX
    ```

## 6.2 Organizarea Stivei
- Stiva crește în jos (spre adrese mai mici) pe arhitectura x86.
- **ESP** (Stack Pointer): Indică vârful stivei.
- **EBP** (Base Pointer): Utilizat pentru referințe la variabile locale și parametri.

### Exemplu de Utilizare
```asm
push eax        ; Salvează EAX pe stivă
mov eax, 10     ; Modifică EAX
pop eax         ; Restaurează valoarea originală a lui EAX
```

## 6.3 Adresarea pe Stivă
- **[ESP]**: Valoarea de pe vârful stivei.
- **[ESP + offset]**: Accesarea elementelor mai jos pe stivă.
- **[EBP - offset]**: Accesarea variabilelor locale.
- **[EBP + offset]**: Accesarea parametrilor funcției.

### Exemplu de Adresare
```asm
mov eax, [ebp + 8] ; Accesează primul parametru al funcției
mov ebx, [ebp - 4] ; Accesează o variabilă locală
```

## 6.4 Note Suplimentare
- Stiva este utilizată pentru apeluri de funcții, salvarea registrelor și alocarea variabilelor locale.
- Este important să restaurați stiva la starea inițială înainte de a reveni dintr-o funcție.

---

# 7. Funcții în ASM

## 7.1 Structura Generală a unei Funcții
1. **Prolog**: Salvează contextul funcției apelante.
2. **Corp**: Execută logica funcției.
3. **Epilog**: Restaurează contextul și revine la apelant.

### Exemplu de Funcție
```asm
my_function:
    push ebp            ; Salvează vechiul EBP
    mov ebp, esp        ; Setează EBP la ESP
    sub esp, 8          ; Alocă 8 octeți pentru variabile locale

    ; Corpul funcției
    mov eax, [ebp + 8]  ; Accesează primul parametru
    add eax, [ebp + 12] ; Adună al doilea parametru

    mov esp, ebp        ; Restaurează ESP
    pop ebp             ; Restaurează vechiul EBP
    ret                 ; Revine la apelant
```

## 7.2 Transmiterea Parametrilor
- Parametrii sunt plasați pe stivă de către apelant, în ordine inversă.
- Exemplu:
  ```asm
  push 5       ; Al doilea parametru
  push 10      ; Primul parametru
  call my_function
  ```

## 7.3 Returnarea Valorilor
- Valorile returnate sunt plasate în registrul **EAX**.
- Exemplu:
  ```asm
  mov eax, 42 ; Returnează valoarea 42
  ret
  ```

## 7.4 Convenții de Apel
- **cdecl**: Apelantul curăță stiva după apelul funcției.
- **stdcall**: Funcția apelată curăță stiva.
- **fastcall**: Primii parametri sunt transmiși prin registre (ex. ECX, EDX).

### Exemplu de Convenție `cdecl`
```asm
push 5
push 10
call my_function
add esp, 8 ; Curăță stiva (2 parametri * 4 octeți)
```

## 7.5 Note Suplimentare
- Este esențial să respectați convenția de apel utilizată pentru a evita coruperea stivei.
- Funcțiile pot utiliza registre temporare, dar trebuie să restaureze registrele salvate înainte de a reveni.

---

# 8. Declararea Antetului unei Funcții

Pentru a declara o funcție în limbaj de asamblare, trebuie să specificăm:
1. Secțiunea de cod în care se află funcția.
2. Numele funcției care poate fi utilizat global.

### Exemplu:
```asm
section .text       ; Secțiunea de cod
global nume_functie ; Exportă funcția pentru a fi utilizată în alte fișiere

nume_functie:
    ; Aici se adaugă instrucțiunile funcției
```

### Explicație:
- **`section .text`**: Specifică secțiunea de cod executabil.
- **`global nume_functie`**: Face funcția `nume_functie` accesibilă din alte fișiere.
- Instrucțiunile funcției sunt plasate sub eticheta `nume_functie`.

---

# 9. Buffer Overflow și Vulnerabilități de Overflow

## 9.1 Ce este un Buffer Overflow?

Un **buffer overflow** (depășire de buffer) apare atunci când un program scrie mai multe date într-un buffer (zonă de memorie alocată pentru date temporare) decât spațiul rezervat pentru acel buffer. Aceasta duce la suprascrierea memoriei adiacente, care poate conține alte variabile, pointeri, adresa de returnare a funcției sau alte date critice.

### De ce este periculos?
- Poate cauza coruperea datelor, comportament neașteptat sau prăbușirea programului.
- Poate permite unui atacator să modifice fluxul execuției programului, de exemplu să execute cod malițios (exploatare).

### Exemplu Simplu (C):

```c
char buf[8];
gets(buf); // Nu verifică dimensiunea, poate suprascrie memoria!
```
Dacă utilizatorul introduce mai mult de 8 caractere, datele suplimentare vor suprascrie memoria de după `buf`. În funcție de layout-ul stivei, acest lucru poate afecta variabile locale, pointeri sau chiar adresa de returnare a funcției.

---

## 9.2 Cum funcționează stiva și de ce este vulnerabilă?

Stiva (stack) este o zonă de memorie folosită pentru:
- Stocarea variabilelor locale ale funcțiilor.
- Stocarea parametrilor funcțiilor.
- Stocarea adresei de returnare (adresa la care trebuie să revină programul după terminarea funcției apelate).
- Salvarea anumitor registre.

### Structura tipică a stivei la apelul unei funcții (de sus în jos, spre adrese mai mici):

```
| ...               |
| parametrii        |  <-- [ebp + 8], [ebp + 12], ...
| adresa returnare  |  <-- [ebp + 4]
| vechiul EBP       |  <-- [ebp]
| variabile locale  |  <-- [ebp - 4], [ebp - 8], ...
| ...               |
```

- La apelul unei funcții, instrucțiunea `call` pune adresa de returnare pe stivă.
- Prologul tipic al unei funcții:
  ```asm
  push ebp
  mov ebp, esp
  sub esp, <dimensiune variabile locale>
  ```
- Variabilele locale sunt plasate sub EBP (la adrese mai mici).

### De ce este vulnerabilă stiva?
Dacă un buffer local (ex: `char buf[8]`) este suprascris cu mai multe date decât spațiul rezervat, datele suplimentare pot ajunge să suprascrie:
- Alte variabile locale.
- Vechiul EBP (poate afecta corectitudinea funcției la revenire).
- **Adresa de returnare**: daca aceasta este suprascrisă, la finalul funcției (`ret`), execuția va sări la adresa specificată de atacator.

---

## 9.3 Exploatarea unui Buffer Overflow

### Pași tipici ai unui atac:
1. Atacatorul identifică un buffer vulnerabil (ex: citire fără limită).
2. Trimite date care depășesc dimensiunea bufferului, astfel încât să suprascrie adresa de returnare.
3. În locul adresei de returnare, scrie adresa unui cod de interes (ex: shellcode sau o funcție existentă, cum ar fi `system("/bin/sh")`).
4. La revenirea din funcție, execuția sare la adresa controlată de atacator.

### Exemplu vizual (buffer de 8 bytes, stivă simplificată):

```
| ...                  |
| parametru            |
| adresa returnare <---|---+  <-- suprascrisă cu adresa dorită
| vechiul EBP          |   |
| buf[0]               |   |
| buf[1]               |   |
| ...                  |   |
| buf[7]               |   |
| ...                  |   |
```
Dacă introduci 12 bytes: primii 8 umplu bufferul, următorii 4 suprascriu vechiul EBP, iar următorii 4 suprascriu adresa de returnare.

### Cod C vulnerabil:
```c
void vuln() {
    char buf[8];
    gets(buf); // Fără limită!
}
```
Dacă apelăm `vuln()` și introducem un șir suficient de lung, putem controla adresa de returnare.

---

## 9.4 Tipuri de overflow și implicații

- **Stack buffer overflow**: cel mai comun, afectează stiva.
- **Heap overflow**: depășirea unui buffer alocat dinamic (`malloc`), poate corupe structuri interne ale heap-ului.
- **Integer overflow**: depășirea valorii maxime a unui tip numeric, poate duce la alocări insuficiente și apoi la buffer overflow.

---

## 9.5 Prevenirea Buffer Overflow

- **Folosirea funcțiilor sigure**: ex. `fgets` în loc de `gets`, `strncpy` în loc de `strcpy`.
- **Verificarea limitelor**: întotdeauna verifică dimensiunea datelor citite/scrise.
- **Protecții la compilare**:
  - **Stack canaries**: valori speciale plasate pe stivă, verificate înainte de `ret`.
  - **DEP (Data Execution Prevention)**: segmentele de date nu pot fi executate ca cod.
  - **ASLR (Address Space Layout Randomization)**: adresele din memorie sunt randomizate la fiecare execuție.
- **Inițializarea bufferelor**: evită scurgerile de informații.
- **Validarea inputului**: nu presupune niciodată că datele de intrare sunt corecte sau sigure.

---

## 9.6 Exemple de funcții vulnerabile și sigure

### Vulnerabil:
```c
void foo() {
    char buf[16];
    gets(buf); // NU folosiți gets!
}
```
### Sigur:
```c
void foo() {
    char buf[16];
    fgets(buf, sizeof(buf), stdin); // Limitează numărul de caractere citite
}
```

---

## 9.7 Rezumat

- Buffer overflow-urile sunt una dintre cele mai vechi și periculoase vulnerabilități software.
- Exploatarea lor poate duce la preluarea controlului asupra programului.
- Înțelegerea structurii stivei și a modului în care funcționează apelurile de funcții este esențială pentru a înțelege și preveni aceste atacuri.
- Scrie întotdeauna cod defensiv și folosește funcții sigure!

---

# 11. Resurse suplimentare

- [Buffer Overflow - Vulnerabilități](https://cs-pub-ro.github.io/hardware-software-interface/labs/lab-10/reading/overflow-vuln.html)
- [Introducere în Buffere](https://cs-pub-ro.github.io/hardware-software-interface/labs/lab-10/reading/buffers-intro.html)

---

# 12. Linking în ASM și C

## 12.1 Ce este Linking-ul?

**Linking-ul** este procesul prin care mai multe fișiere obiect (obținute prin compilarea surselor .c sau .asm) sunt combinate pentru a produce un executabil final. Linking-ul rezolvă referințele între simboluri (funcții, variabile) definite în fișiere diferite.

Procesul de linking poate fi:
- **Static**: toate fișierele obiect sunt combinate într-un singur executabil, fără dependențe externe la rulare.
- **Dinamic**: unele simboluri sunt rezolvate la rulare, folosind biblioteci dinamice (.so, .dll).

---

## 12.2 Etapele Linking-ului

1. **Compilare/Asamblare**: Fiecare fișier sursă este transformat într-un fișier obiect (.o), care conține cod mașină și tabele de simboluri.
2. **Linking**: Linker-ul combină fișierele obiect, rezolvă simbolurile externe și creează executabilul.

---

## 12.3 Simboluri și Rezolvarea lor

- **Simboluri definite**: funcții sau variabile implementate într-un fișier.
- **Simboluri nerezolvate (externe)**: funcții sau variabile folosite, dar nedefinite în fișierul curent.

Linker-ul caută pentru fiecare simbol nerezolvat o definiție într-un alt fișier obiect sau într-o bibliotecă.

---

## 12.4 Linking Static vs. Linking Dinamic

### Linking Static
- Toate bibliotecile și codul sunt incluse în executabil.
- Executabilul este independent, dar mai mare.
- Exemplu: `gcc main.o utils.o -o program`

### Linking Dinamic
- Unele funcții/variabile sunt rezolvate la rulare, din biblioteci partajate (.so pe Linux, .dll pe Windows).
- Executabilul este mai mic, dar depinde de bibliotecile dinamice la rulare.
- Exemplu: `gcc main.o -o program -lm` (leagă cu libm.so)

---

## 12.5 Cum se face linking-ul în ASM

- Pentru a folosi funcții definite în alte fișiere, se folosește directiva `extern` pentru simboluri externe și `global` pentru simboluri exportate.
- Exemplu:
  - În `main.asm`:
    ```asm
    extern functie_externa
    global main

    section .text
    main:
        call functie_externa
        ret
    ```
  - În `functie.asm`:
    ```asm
    global functie_externa

    section .text
    functie_externa:
        ; codul funcției
        ret
    ```
- Asamblare și linking:
  ```
  nasm -f elf32 main.asm
  nasm -f elf32 functie.asm
  ld -m elf_i386 main.o functie.o -o program
  ```

---

## 12.6 Simboluri și Vizibilitate

- **global**: face simbolul vizibil pentru linker (poate fi folosit din alte fișiere).
- **extern**: declară că simbolul este definit în alt fișier.
- Simbolurile locale (fără global) nu pot fi accesate din alte fișiere.

---

## 12.7 Erori frecvente la linking

- **Undefined reference**: simbolul nu a fost găsit în niciun fișier obiect sau bibliotecă.
- **Multiple definition**: același simbol este definit în mai multe fișiere.
- **Order of linking**: la linking dinamic, ordinea bibliotecilor contează (ex: `-lm` la final pentru funcții matematice).

---

## 12.8 Exemple de linking între C și ASM

- Poți apela funcții ASM din C și invers, dacă respecți convenția de apel și declari corect simbolurile.
- În C:
  ```c
  extern int asm_func(int a, int b);
  int main() {
      return asm_func(2, 3);
  }
  ```
- În ASM:
  ```asm
  global asm_func
  section .text
  asm_func:
      ; codul funcției
      ret
  ```

---

## 12.9 Instrumente utile

- **nm**: afișează simbolurile dintr-un fișier obiect sau executabil.
- **ldd**: afișează bibliotecile dinamice folosite de un executabil.
- **objdump**: inspectează conținutul fișierelor obiect/executabile.

---

## 12.10 Resurse suplimentare

- [Linking - Hardware Software Interface](https://cs-pub-ro.github.io/hardware-software-interface/labs/lab-11/reading/linking.html)

---

## Exemplu de payload pentru buffer overflow

O comandă Python utilă pentru testarea buffer overflow-urilor:

```bash
python3 -c 'import sys; sys.stdout.buffer.write(b"A" * 24 + b"\xa6\x91\x04\x44")'
```

**Explicație:**
- Creează un șir de 24 de caractere "A" (24 bytes cu valoarea 0x41).
- Adaugă la final 4 bytes cu valorile hexazecimale: 0xa6, 0x91, 0x04, 0x44.
- Scrie acest șir binar direct la ieșirea standard (stdout), fără conversie la text.
- Se folosește pentru a umple un buffer și a suprascrie adresa de returnare (de exemplu, la exploatarea unui buffer overflow).
- Adresa 0x440491a6 este scrisă în format little-endian (octeții inversați față de cum apar în memorie).

**Scop:**
- Testarea vulnerabilităților de tip buffer overflow, suprascrierea adresei de returnare sau a altor date critice pe stivă.

---

## Exemple suplimentare de payload pentru buffer overflow

### 1. Suprascrierea adresei de returnare cu adresa unei funcții (ex: win)

Dacă vrei să redirecționezi execuția către o funcție existentă (de exemplu, `win()`), trebuie să determini offset-ul până la adresa de returnare și să folosești adresa funcției în format little-endian.

```bash
python3 -c 'import sys; sys.stdout.buffer.write(b"A" * <offset> + b"\x<adresa_win>")'
```

**Exemplu concret:**
Dacă offset-ul este 40 și adresa funcției `win` este `0x080491b6`:
```bash
python3 -c 'import sys; sys.stdout.buffer.write(b"A" * 40 + b"\xb6\x91\x04\x08")'
```

### 2. Payload cu shellcode (execve /bin/sh)

Poți injecta shellcode direct în buffer, urmat de padding și adresa de returnare care să pointeze înapoi în buffer (sau într-un NOP sled).

```bash
python3 -c 'import sys; sys.stdout.buffer.write(b"\x90"*16 + b"<shellcode>" + b"A"*(<offset>-<shellcode_len>-16) + b"<adresa_buffer>")'
```

**Exemplu simplificat:**
```bash
python3 -c 'import sys; sys.stdout.buffer.write(b"\x90"*16 + b"\x31\xc0..." + b"A"*8 + b"\x40\xf0\xff\xbf")'
```
- `\x90`*16: NOP sled (instrucțiuni NOP pentru a crește șansa de execuție corectă)
- `<shellcode>`: codul shellcode (ex: execve /bin/sh)
- `A`*8: padding până la adresa de returnare
- `\x40\xf0\xff\xbf`: adresa de start a bufferului (exemplu, trebuie determinată cu gdb)

### 3. Cum determini offset-ul și adresa corectă
- Folosește un pattern generator (ex: `pwntools`, `pattern_create.rb`) pentru a determina offset-ul exact până la adresa de returnare.
- Adresa de returnare trebuie să fie în format little-endian (octeții inversați).
- Adresa funcției sau a bufferului se poate afla cu `nm`, `objdump`, sau cu un debugger (`gdb`).

### Notă de securitate
- Aceste exemple sunt pentru scopuri educaționale! Nu folosiți tehnici de exploatare pe sisteme fără permisiune.

---

# Sfaturi esențiale pentru programarea în ASM (x86)

1. **Registrele și utilizarea lor**
   - Cunoaște registrele generale (EAX, EBX, ECX, EDX), de index (ESI, EDI), de stivă (ESP, EBP), de segment și de control.
   - Înțelege accesul parțial la registre (ex: AL, AH, AX, EAX).

2. **Instrucțiuni de bază**
   - `mov`, `push`, `pop`, `lea`, `xchg` – mutarea și manipularea datelor.
   - Instrucțiuni aritmetice și logice: `add`, `sub`, `and`, `or`, `xor`, `test`.

3. **Stiva și apelul de funcții**
   - Cum funcționează stiva (crește în jos, ESP/EBP).
   - Prolog/epilog de funcție, transmiterea parametrilor, returnarea valorilor.
   - Convenții de apel (cdecl, stdcall, fastcall).

4. **Salturi și controlul fluxului**
   - Salturi necondiționate și condiționate (`jmp`, `je`, `jne`, etc.).
   - Utilizarea flag-urilor și instrucțiunilor de comparație (`cmp`, `test`).

5. **Directive de date și organizarea memoriei**
   - Definirea datelor cu `db`, `dw`, `dd`, `resb`, `resw`, `resd`.
   - Diferența dintre `.data`, `.bss`, `.text`, `.rodata`.

6. **Structuri și vectori**
   - Definirea și accesarea structurilor și vectorilor.
   - Calculul offset-urilor manual.

7. **Linking și vizibilitatea simbolurilor**
   - `global`, `extern`, linking între fișiere ASM sau ASM+C.
   - Erori frecvente la linking.

8. **Vulnerabilități și siguranță**
   - Cum apar buffer overflow-urile și cum pot fi prevenite.
   - Importanța validării inputului și folosirii funcțiilor sigure.

9. **Debugging și instrumente**
   - Folosește `gdb`, `objdump`, `nm`, `ldd` pentru analiză și depanare.
   - Înțelege layout-ul stivei și al memoriei pentru debugging.

10. **Optimizare și detalii avansate**
    - Folosește instrucțiuni speciale pentru performanță (`imul`, `idiv`, instrucțiuni SIMD).
    - Atenție la alinierea datelor și la structuri (padding).
    - Înțelege efectul side-effect-urilor și al hazardelor de date.

# Macro util: PRINTF32

Un macro frecvent folosit pentru afișarea unui număr întreg pe 32 de biți (din EAX) folosind printf din C:

```asm
PRINTF32  `%d\n\x0`, eax
```

**Utilizare:**
- Se folosește pentru a afișa rapid valoarea din registrul EAX ca întreg zecimal, urmat de newline.
- Exemplu de apel în cod ASM (NASM, cu interfață C):
  ```asm
  push eax
  push format_int
  call printf
  add esp, 8
  ; unde format_int db '%d', 10, 0
  ```
- Poți folosi macro-ul pentru a evita scrierea repetată a formatului și a registrului.

**Notă:**
- Macro-ul presupune că folosești convenția de apel C și că ai definit formatul de string corespunzător în secțiunea de date.

---
