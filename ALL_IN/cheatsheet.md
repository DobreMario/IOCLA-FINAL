# ASM x86 Cheatsheet

## Macro util

```
PRINTF32  `%d\n\x0`, eax
```

---

## Citire cu scanf (cu lea)

```asm
section .data
    format_int db '%d', 0
    var dd 0

section .text
    lea eax, [var]
    push eax
    push format_int
    call scanf
    add esp, 8
```

---

## Accesarea elementelor din array (byte, word, dword, movzx)

```asm
section .data
    arr db 1, 2, 3, 4
    arrw dw 1000, 2000, 3000
    arrd dd 100000, 200000

section .text
    ; Byte
    mov al, [arr + 2]        ; al = arr[2]
    movzx eax, byte [arr+2]  ; eax = arr[2] (zero-extend)

    ; Word
    mov ax, [arrw + 2*1]     ; ax = arrw[1]
    movzx eax, word [arrw+2*1] ; eax = arrw[1] (zero-extend)

    ; Dword
    mov eax, [arrd + 4*1]    ; eax = arrd[1]

    ; Caz general (movzx pentru orice mărime)
    movzx eax, byte [arr+idx]   ; idx = index
    movzx eax, word [arrw+2*idx]
```

---

## Schelet for

```asm
mov ecx, n      ; n = număr de iterații
for_loop:
    ; ... cod ...
    loop for_loop
```

## Schelet while

```asm
while_start:
    ; ... cod pentru condiție ...
    cmp eax, ebx
    jne while_start
    ; ... cod ...
    jmp while_start
```

## Schelet if

```asm
    cmp eax, ebx
    je  equal_label
    ; ... cod dacă NU e egal ...
    jmp end_if
  equal_label:
    ; ... cod dacă e egal ...
  end_if:
```

---

## Exemplu înmulțire și împărțire

```asm
; Înmulțire fără semn (8, 16, 32 biți)
mov al, 5
mov bl, 10
mul bl        ; AX = AL * BL

mov ax, 1000
mov bx, 3
mul bx        ; DX:AX = AX * BX
; Dacă vrei să combini DX și AX într-un singur registru de 32 biți (rezultat_32 = (DX << 16) | AX):
mov cx, dx    ; păstrăm DX
shl ecx, 16   ; mutăm DX pe poziția high (bit 16-31)
or  cx, ax    ; combinăm cu AX (bit 0-15)
; Acum CX conține rezultatul pe 32 biți (atenție: pentru valori mari, folosește un registru de 32 biți)

mov eax, 5
mov ebx, 10
mul ebx       ; EDX:EAX = EAX * EBX

; Împărțire fără semn (8, 16, 32 biți)
mov ax, 100
mov bl, 7
div bl        ; AL = AX / BL, AH = AX % BL

mov dx, 0
mov ax, 1000
mov bx, 7
div bx        ; AX = (DX:AX) / BX, DX = rest

mov edx, 0
mov eax, 1000
mov ebx, 7
div ebx       ; EAX = (EDX:EAX) / EBX, EDX = rest
```

---

## Tabel mappare registre

|  31 ... 16  | 15 ... 8 | 7 ... 0 |
|:-----------:|:--------:|:-------:|
|     EAX     |   AH     |   AL    |
|     EBX     |   BH     |   BL    |
|     ECX     |   CH     |   CL    |
|     EDX     |   DH     |   DL    |

- AX, BX, CX, DX = partea de jos (16 biți) din EAX, EBX, ECX, EDX
- AH/AL = high/low byte din AX etc.

---

## Variabile locale pe stivă și organizarea funcțiilor

### Cum se folosesc variabilele locale în ASM (x86)

- Variabilele locale se alocă pe stivă, de obicei în prologul funcției:

```asm
push ebp            ; salvează vechiul EBP
mov ebp, esp        ; stabilește cadrul funcției
sub esp, N          ; alocă N bytes pentru variabile locale
```
- Accesul la variabile locale se face relativ la EBP:
  - `[ebp-4]`, `[ebp-8]` etc. (N = 4 pentru int, 1 pentru char)
- La finalul funcției, se restaurează cadrul:

```asm
mov esp, ebp
pop ebp
ret
```

### Exemplu simplu cu variabilă locală (int):

```asm
push ebp
mov ebp, esp
sub esp, 4         ; rezervă 4 bytes pentru o variabilă locală
mov dword [ebp-4], 42   ; variabila locală = 42
; ... folosește [ebp-4] ...
mov esp, ebp
pop ebp
ret
```

---

## Container pentru subrutine: izolare cu pusha/popa

- Pentru a salva TOATE registrele generale la începutul unei funcții sau bloc de cod ("container"), folosește:

```asm
pusha      ; salvează toate registrele generale pe stivă
; ... cod ...
popa       ; restaurează toate registrele
```
- Astfel, orice modificare a registrelor în subrutina ta nu va afecta apelantul.
- Recomandat pentru funcții utilitare sau cod care nu trebuie să strice contextul apelantului.

### Exemplu schemă funcție cu variabile locale și pusha/popa

```asm
my_func:
    push ebp
    mov ebp, esp
    sub esp, 8      ; rezervă 8 bytes pentru variabile locale
    pusha           ; salvează toate registrele
    ; ... cod ...
    popa            ; restaurează toate registrele
    mov esp, ebp
    pop ebp
    ret
```
- Accesezi variabilele locale cu `[ebp-4]`, `[ebp-8]` etc.
- Poți folosi această schemă pentru orice subrutine unde vrei izolare completă a contextului.

---

## Exploatare buffer overflow: generare payload cu python3

- Pentru a genera rapid un payload pentru buffer overflow (inclusiv shellcode, padding și adresa de return), poți folosi:

```bash
python3 -c 'import sys; sys.stdout.buffer.write(b"\x90"*16 + b"\x31\xc0..." + b"A"*8 + b"\x40\xf0\xff\xbf")'
```
- Explicație:
  - `b"\x90"*16` — NOP sled (16 instrucțiuni NOP)
  - `b"\x31\xc0..."` — shellcode (înlocuiește cu shellcode-ul dorit)
  - `b"A"*8` — padding pentru a ajunge la adresa de return
  - `b"\x40\xf0\xff\xbf"` — adresa de return (little-endian)
- Poți ajusta dimensiunile și shellcode-ul după nevoie.

---

## Operații pe biți: rest și împărțire rapidă la 2^n

- Pentru a calcula restul împărțirii la 2^n (ex: restul la 8, 16, 32):

```asm
and eax, 7      ; eax = eax % 8 (2^3-1)
and eax, 15     ; eax = eax % 16 (2^4-1)
```
- Pentru împărțire rapidă la 2^n (ex: împărțire la 8, 16, 32):

```asm
shr eax, 3      ; eax = eax / 8
shr eax, 4      ; eax = eax / 16
```
- Aceste tehnici sunt mult mai rapide decât instrucțiunile clasice de div.

---

## Unelte utile pentru analiză binară și debugging

### nm
- Afișează simbolurile (funcții, variabile) dintr-un fișier obiect sau executabil.
- Exemplu:

```bash
nm program.o
nm ./executabil
```
- Poți vedea ce funcții/variabile sunt definite sau doar declarate (U = undefined, T = text/code, D = data etc.).

### ldd
- Afișează bibliotecile dinamice folosite de un executabil (ce .so-uri sunt încărcate la rulare).
- Exemplu:

```bash
ldd ./executabil
```
- Vezi rapid de ce biblioteci depinde un program.

### objdump
- Inspectează conținutul fișierelor obiect sau executabile (inclusiv dezasamblare, secțiuni, headere etc.).
- Exemple utile:

```bash
objdump -d ./executabil      # dezasamblează codul (instrucțiuni ASM)
objdump -D -M intel ./executabil  # dezasamblează TOT codul, cu sintaxă Intel (mai ușor de citit)
objdump -x ./executabil      # afișează headere și secțiuni
objdump -s ./executabil      # afișează conținutul secțiunilor (hex)
```
- `-D -M intel` este comanda „magică” pentru a vedea tot codul dezasamblat, cu sintaxă familiară Intel.
- Poți folosi și pentru fișiere .o, .so, .a etc.

---

