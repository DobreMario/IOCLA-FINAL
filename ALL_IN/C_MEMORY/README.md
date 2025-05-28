# Zonele de memorie în C: data, bss, stack, heap, rodata

Într-un program C, variabilele și datele sunt plasate în diferite zone de memorie, fiecare cu rolul și proprietățile ei. Înțelegerea acestor zone este esențială pentru optimizare, debugging și securitate.

---

## 1. .data (Data Segment)
- **Ce conține:** variabile globale/statice inițializate explicit (ex: `int x = 5;`).
- **Proprietăți:**
  - Există pe toată durata execuției programului.
  - Valoarea inițială este stocată în fișierul executabil.
  - Variabilele pot fi modificate la runtime.
- **Exportabilitate:**
  - Variabilele globale pot fi exportate (vizibile în alte fișiere cu `extern`).
  - Variabilele statice la nivel de fișier NU sunt exportabile (au vizibilitate doar în fișierul sursă).
- **Exemplu:**
  ```c
  int a = 10;         // globală, exportabilă
  static int b = 20;  // statică, nu e exportabilă
  ```

---

## 2. .bss (Block Started by Symbol)
- **Ce conține:** variabile globale/statice neinițializate sau inițializate cu 0 (ex: `int x;`).
- **Proprietăți:**
  - Există pe toată durata execuției programului.
  - Inițializate implicit cu 0 la start.
  - Nu ocupă spațiu în fișierul executabil (doar dimensiunea este notată).
- **Exportabilitate:**
  - La fel ca la `.data` (globale vs. statice).
- **Exemplu:**
  ```c
  int c;             // globală, exportabilă
  static int d;      // statică, nu e exportabilă
  ```

---

## 3. .rodata (Read-Only Data)
- **Ce conține:** date constante, stringuri constante (ex: `const char *msg = "Hello";`).
- **Proprietăți:**
  - Datele nu pot fi modificate la runtime (scrierea duce la segfault).
  - Util pentru optimizare și protecție.
- **Exportabilitate:**
  - Variabilele `const` la nivel global pot fi exportate, dar nu pot fi modificate.
- **Exemplu:**
  ```c
  const char *msg = "Salut!"; // string constant în .rodata
  const int e = 42;            // constant global
  ```

---

## 4. Stack (Stivă)
- **Ce conține:** variabile locale, parametri funcții, adrese de returnare.
- **Proprietăți:**
  - Alocare/dezalocare automată (LIFO).
  - Dimensiune limitată (stack overflow dacă depășești limita).
  - Variabilele nu sunt exportabile (vizibile doar în funcția curentă).
- **Exemplu:**
  ```c
  void foo() {
      int x = 5; // variabilă locală pe stivă
  }
  ```

---

## 5. Heap
- **Ce conține:** date alocate dinamic la runtime (`malloc`, `calloc`, `realloc`).
- **Proprietăți:**
  - Alocare/dezalocare manuală (cu `malloc`/`free`).
  - Dimensiune mare (limitată de RAM/sistem).
  - Variabilele nu sunt exportabile (accesibile doar prin pointeri).
- **Exemplu:**
  ```c
  int *p = malloc(sizeof(int) * 10); // vector de 10 int pe heap
  free(p);
  ```

---

## 6. TLS (Thread-Local Storage)
- **Ce conține:** variabile declarate cu `__thread` sau `thread_local` (C11), fiecare thread are propria instanță.
- **Proprietăți:**
  - Utilizate în programarea multithreaded.
  - Fiecare thread are o copie a variabilei, izolată de celelalte threads.
- **Exemplu:**
  ```c
  __thread int tls_var = 0; // variabilă TLS, fiecare thread are propria instanță
  ```

---

## 7. Text/Code Segment
- **Ce conține:** codul executabil (funcțiile).
- **Proprietăți:**
  - Este de obicei doar citire (pentru a preveni modificarea codului la runtime).
  - Dimensiunea este determinată la compilare.
- **Exemplu:**
  ```c
  void foo() {
      // codul funcției
  }
  ```

---

## 8. Mapped Memory
- **Ce conține:** zone de memorie mapate explicit (ex: cu `mmap`), folosite pentru fișiere, partajare între procese etc.
- **Proprietăți:**
  - Alocare/dezalocare manuală.
  - Poate fi folosită pentru a mapa fișiere întregi în memorie.
- **Exemplu:**
  ```c
  void *mapped = mmap(...); // mapare memorie
  ```

---

## Rezumat tabelar
| Zonă     | Exemplu variabilă         | Inițializare | Durată      | Exportabilă | Modificabilă |
|----------|--------------------------|--------------|-------------|-------------|--------------|
| .data    | int a = 1;               | Da           | Globală     | Da          | Da           |
| .bss     | int b;                   | Nu           | Globală     | Da          | Da           |
| .rodata  | const char *s = "abc";   | Da (const)   | Globală     | Da          | Nu           |
| stack    | int x; (în funcție)      | Da           | Locală      | Nu          | Da           |
| heap     | malloc(...);             | Da           | Dinamică    | Nu          | Da           |
| TLS      | __thread int t;          | Da           | Thread-local| Nu          | Da           |
| text     | void foo() {}            | N/A          | N/A         | Nu          | Nu           |
| mapped    | mmap(...);               | N/A          | La runtime  | Nu          | Nu           |

---

## Alte detalii
- Variabilele `static` la nivel de fișier nu sunt exportabile (au vizibilitate doar în fișier).
- Variabilele `const` pot fi puse în `.rodata` (dacă nu sunt modificate).
- Variabilele locale statice (`static int x;` în funcție) sunt în `.data` sau `.bss`, dar vizibile doar în funcție.
- Stringurile literale sunt de obicei în `.rodata`.

---

## Exemplu practic: variabile în toate zonele de memorie

```c
#include <stdio.h>
#include <stdlib.h>

// .rodata (read-only data, globală, exportabilă)
export const int exportable_rodata = 1234;
const char *global_rodata = "Salut din .rodata!";
const int global_const = 42;

// .rodata (read-only data, static, neexportabilă)
static const int static_rodata = 99;

// .data (data segment, globală, exportabilă)
int global_data = 10;

// .data (data segment, static, neexportabilă)
static int static_data = 7;

// .bss (uninitialized data, globală, exportabilă)
int global_bss;

// .bss (uninitialized data, static, neexportabilă)
static int static_bss;

// .tdata / .tbss (Thread-Local Storage)
__thread int tls_data = 55;           // .tdata (TLS, inițializată)
__thread int tls_bss;                 // .tbss (TLS, neinițializată)

// stack (locală, doar în funcție)
// int local_stack = 5;

// stack (locală statică, doar în funcție, dar în .data/.bss)
// static int local_static = 99;

// heap (alocare dinamică, doar în funcție)
// int *heap_var = malloc(sizeof(int));

// text/code segment (funcții)
// void foo() {} // funcțiile sunt plasate în segmentul de cod (text)

// mapped memory (ex: mmap, doar la runtime)
// void *mapped = mmap(...);
```

---

## Alte variante/zone
- **TLS (Thread-Local Storage):** variabile declarate cu `__thread` sau `thread_local` (C11), fiecare thread are propria instanță.
- **Text/code segment:** conține codul executabil (funcțiile). Nu poți declara variabile aici, dar funcțiile sunt plasate în această zonă.
- **Mapped memory:** zone de memorie mapate explicit (ex: cu `mmap`), folosite pentru fișiere, partajare între procese etc.

În mod uzual, pentru variabile în C, cele mai importante zone sunt cele prezentate mai sus: .data, .bss, .rodata, stack, heap, plus TLS pentru programare multithreaded.

---

## Resurse
- [Memory Layout of C Programs (GeeksforGeeks)](https://www.geeksforgeeks.org/memory-layout-of-c-program/)
- [C Data Segments (Wikipedia)](https://en.wikipedia.org/wiki/Data_segment)
