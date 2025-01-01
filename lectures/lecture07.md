<!--
---
marp: true
footer: "| PwJC | Wykład #7 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/"
_footer: "| e-mail: mkepski@ur.edu.pl |"
paginate: true
_paginate: false
---
-->

# programowanie w języku C
### Wykład #7: Pamięć wirtualna. Typowe błędy w C.

*dr inż. Michał Kępski* 

---

# Pamięć wirtualna

Pamięć wirtualna to mechanizm, który daje każdemu procesowi złudzenie, że ma wyłączny dostęp do pamięci operacyjnej. Każdy proces ma taki sam jednolity obraz pamięci, nazywany jego wirtualną przestrzenią adresową.

---

## Pamięć wirtualna procesu w systemie Linux:

<!--
<style scoped>
h2 { margin-bottom: -6px; }
pre {background-color: #ffffffff; border-color: #ffffffff}
</style>
-->

```text
Wysokie adresy ---> .---------------------------. 
                    |                           |
                    |            STOS           |    
base pointer ->     |- - - - - - - - - - - - - -|   wskaźnik stosu
                    |             |             |                        
                    :             v             :
                    .                           . 
                    |---------------------------|
                    |      Mapowana pamięć      | 
                    | bibliotek współdzielonych | 
                    |---------------------------|   
                    .                           .   
                    :                           :    
                    |             ^             |
                    |             |             |
        brk  ->     |- - - - - - - - - - - - - -|   góra sterty
                    |          STERTA           |
                    |---------------------------|
                    |           .bss            |   dane niezainicjowane
                    |---------------------------|   
                    |           .data           |   dane zainicjowane
                    |---------------------------|
                    |           .text           |   kod binarny
                    |---------------------------|
                 0  '---------------------------'
```

---

## Program text (```.text```)

- Zawiera kod programu, czyli instrukcje maszynowe.

- Jest to część pamięci tylko do odczytu (ang. *read-only*), co zapobiega przypadkowej modyfikacji kodu programu.

- Sekcja ```.text``` jest umieszczona na dole przestrzeni adresowej.

---

## Initialized data (```.data```)

Przechowuje dane statyczne i globalne, które zostały zainicjalizowane przez program.

Przykład: ```int x = 10;``` (zmienna globalna lub statyczna o określonej wartości początkowej).

## Uninitialized data (```.bss```)

Sekcja .bss zawiera zmienne globalne i statyczne, które nie zostały zainicjalizowane (lub zostały zainicjalizowane do zera).

Przykład: ```int y```; (zmienna globalna lub statyczna bez przypisanej wartości).

---

## Sterta (*heap*)

Obszar pamięci dynamicznej, który jest używany do alokacji pamięci w czasie działania programu.

Funkcje takie jak ```malloc``` i ```free``` w języku C (lub ich odpowiedniki w innych językach) operują na tej części pamięci.

Sterta rośnie w górę, zaczynając od granicy sekcji ```.bss```, a jej rozmiar jest kontrolowany przez wskaźnik ```brk```.

---

## Mapowana pamięć bibliotek współdzielonych

Obszar pamięci przeznaczony dla bibliotek współdzielonych (ang. *shared libraries*) oraz innych mapowanych obszarów pamięci (np. plików).

Biblioteki współdzielone są ładowane tutaj, aby mogły być używane przez wiele procesów bez duplikacji w pamięci fizycznej.

---

## Stos użytkownika (ang. *user stack*)

Przestrzeń pamięci dla stosu, która przechowuje ramki wywołań funkcji, zmienne lokalne i inne dane kontekstowe funkcji.

Ramka funkcji jest usuwana ze stosu po zakończeniu wywołania funkcji.

Stos rośnie w dół, zaczynając od najwyższych adresów pamięci w przestrzeni adresowej procesu.

W przypadku przepełnienia stosu może dojść do błędu zwanego *stack overflow*.

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |      argc: 1      |
    int c = 100;                                     |      argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |                   |
                                                     |                   |
int main(int argc, char *argv[]) {   <---            |                   |
    int a = 39;                                      |                   | 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

*Przykład pochodzi z kursu CS107: Computer Organization & Systems, Stanford University, https://web.stanford.edu/class/cs107/*

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |      argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |                   |
                                                     |                   |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                      <---            |                   | 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |                   |
                                                     |                   |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |                   | 
    int b = 10;                      <---            |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |         |         |
                                                     |         v         |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |                   | 
    int b = 10;                                      |                   |
    f1();                            <---            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                          <---            |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |f1                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                     <---            |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |f1                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |c:100              |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                            <---            |___________________|
}                                                    |f1                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |c:100              |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |         |         |
    f1();                                            |         v         |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                          <---            |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |f1                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |c:100              |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |f2                 |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |___________________|
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                       <---            |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |f1                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |c:100              |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |f2                 |
    f1();                                            |                   |
    f2();                                            |d:0                |
    printf("Finished!");                             |___________________|
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                    <---            |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |f1                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |c:100              |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |f2                 |
    f1();                                            |                   |
    f2();                                            |d:0                |
    printf("Finished!");                             |___________________|
    return 0;                                        |         ^         |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                    <---            |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |f1                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |c:100              |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                    <---            |f1                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |c:100              |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |         ^         |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                    <---            |                   |
                                                     |                   |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |                   |
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |         |         |
                                                     |         V         |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |                   |
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                            <---            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                          <---            |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |f2                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                       <---            |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |f2                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |d:0                |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                    <---            |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |f2                 |
                                                     |                   |
int main(int argc, char *argv[]) {                   |d:0                |
    int a = 39;                                      |___________________| 
    int b = 10;                                      |         ^         |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |                   |
                                                     |                   |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |                   | 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");            <---             |                   |
    return 0;                                        |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |                   |
                                                     |                   |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |                   | 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                       <---             |                   |
}                                                    +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |___________________|   
    int d = 0;                                       |main               |
}                                                    |                   |
void f1() {                                          |a:39  argc: 1      |
    int c = 100;                                     |b:10  argv: 0x7ff..|
    f2();                                            |___________________|
}                                                    |         ^         |
                                                     |                   |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |                   | 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                   <---             +-------------------+ 
```

---

<!--
<style scoped> 
pre {font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {font-size: 14pt;}
</style>
-->

## Stos użytkownika (ang. *user stack*)

```c
#include <stdio.h>                                            STOS
                                                     +-------------------+
void f2() {                                          |                   |
    int d = 0;                                       |                   |
}                                                    |                   |
void f1() {                                          |                   |
    int c = 100;                                     |                   |
    f2();                                            |                   |
}                                                    |                   |
                                                     |                   |
int main(int argc, char *argv[]) {                   |                   |
    int a = 39;                                      |                   | 
    int b = 10;                                      |                   |
    f1();                                            |                   |
    f2();                                            |                   |
    printf("Finished!");                             |                   |
    return 0;                                        |                   |
}                                   <---             +-------------------+ 
```

---

<!--
<style scoped> 
pre { font-size: 20pt;  background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
</style>
-->

## Przypadek kiedy tworzenie zmiennych na stosie zawodzi

```c
#include <stdio.h>

char *create_string(char ch, int num) { 
    char new_str[num + 1];
    for (int i = 0; i < num; i++) {
        new_str[i] = ch; 
    }
    new_str[num] = '\0';
    return new_str; 
}

int main(int argc, char *argv[]) { 
    char *str = create_string('a', 4); 
    printf("%s", str); // want "aaaa" 
    return 0;
}
```
**Dlaczego to nie zadziała?**

---

<!--
<style scoped> 
pre { font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {margin-top: -38px;}
</style>
-->

## Przypadek kiedy tworzenie zmiennych na stosie zawodzi

```c
#include <stdio.h>                                         STOS
                                                  +---------------------+
char *create_string(char ch, int num) {           |_____________________|
    char new_str[num + 1];                        |main                 |
    for (int i = 0; i < num; i++) {               |                     |
        new_str[i] = ch;                          |argc:1  argv:0x7ff.. |
    }                                             |        str:0xd03a6..|<.
    new_str[num] = '\0';                          |_____________________| :
    return new_str;                       <---    |create_string        | :
}                                                 |                     | :
                                                  |ch:'a'   num:3       | :
int main(int argc, char *argv[]) {                |                     | : 
    char *str = create_string('a', 3);            |         '\0'        | :
    printf("%s", str);                            |         'a'         | : 
    return 0;                                     |         'a'         | :
}                                                 | new_str:'a'  .......|.:
                                                  |_____________________|
                                                  |                     |
                                                  +---------------------+ 
```      
**Zwracamy adres łańcucha tekstowego z ramki stosu, która zaraz zostanie usunięta!** 

---

<!--
<style scoped> 
pre { font-size: 20pt; background-color: #ffffffff; border-color: #ffffffff; line-height: 1;}
p {margin-top: -20px;}
</style>
-->

## Rozwiązanie? ```malloc``` :two_hearts:

```c
#include <stdio.h>
#include <stdlib.h>

char *create_string(char ch, int num) {
    char *new_str = malloc(num + 1);
    for (int i = 0; i < num; i++) { 
        new_str[i] = ch;
    }
    new_str[num] = '\0'; return new_str;
}

int main(int argc, char *argv[]) { 
    char *str = create_string('a', 3); 
    printf("%s", str); 
    return 0;
}
```
Tablica tworzona jest na stercie, będzie tam dopóki nie zwolnimy pamięci funkcją ```free()```.

---

## Wskazówki dot. używania ```free()```

- Gdy nie potrzebujemy zaalokowanej pamięci, odpowiadamy za to, aby ją zwolnić.
- Nawet jeśli mamy wiele wskaźników wskazujących na ten sam blok pamięci, każdy blok pamięci powinien zostać zwolniony tylko raz.
- Musimy zwolnić dokładnie ten adres, który dostaliśmy podczas wywołania funkcji alokującej pamięć (a nie tylko adres części przydzielonej pamięci). Robimy więc tak:
   ```c
   char *ptr = malloc(10);
   free(ptr);
   ```
   a nie np.:
   ```c
   free(ptr + 1);
   ```

---

## Wskazówki dot. używania ```free()```

Wyciekiem pamięci nazywamy sytuację, gdy nie zwalniamy wcześniej zaalokowanej pamięci:

```c
char *str = strdup("hello");
str = strdup("hi"); 
```

- Pierwsze wywołanie ```strdup``` alokuje pamięć na stercie i przypisuje adres do ```str```.
- Drugie wywołanie ```strdup``` alokuje nowy blok pamięci i przypisuje jego adres do ```str```, nadpisując poprzedni adres.
- Poprzednio zaalokowana pamięć (dla ```"hello"```) zostaje utracona, ponieważ nie ma już wskaźnika wskazującego na nią, a więc nie można jej zwolnić za pomocą ```free()```.

---

# Typowe błędy (początkujących) w C

*Opracowane na podstawie: http://pacman128.github.io/internal/common_c_errors/*

---

## Zapomninanie o ```break``` w instrukcji ```switch```


W języku C instrukcja ```switch``` nie przerywa automatycznie po napotkaniu pasującego ```case```:

```c
#include <stdio.h>

int main() {
    int x = 1;
    switch(x) {
        case 1: printf("Jeden ");
        case 2: printf("Dwa ");
        default: printf("Domyślny");
    }
    return 0;
}
```

```text
Jeden Dwa Domyślny
```

---

## Zapomninanie o ```break``` w instrukcji ```switch```

Rozwiązanie:

```c
#include <stdio.h>

int main() {
    int x = 1;
    switch(x) {
        case 1: printf("Jeden "); break;
        case 2: printf("Dwa "); break;
        default: printf("Domyślny"); 
    }
    return 0;
}
```
```text
Jeden
```

---

## Użycie ```=``` zamiast ```==```

Operator ```=``` w języku C służy wyłącznie do przypisania wartości i zwraca przypisaną wartość.
Z kolei operator ```==``` jest używany do porównań i zwraca wartość całkowitą: ```0``` dla fałszu lub ```1``` dla prawdy.

Z tego powodu kompilator C nie zgłasza błędu, gdy przez przypadek użyjemy ```=``` zamiast ```==```, mimo że prawdopodobnie chcieliśmy porównać wartości:

```c
int x = 5;
if (x = 6)                // nadpisaliśmy, a prawdopodobnie chcieliśmy porównać
    printf("x equals 6"); // wykona się, bo przypisanie zwraca 6 (true)
```

---

## Jak zapobiec przypadkowemu ```=``` zamiast ```==``` w ```if```?

- Uważnie używaj operatorów ```=``` i ```==```.
- W niektórych przypadkach można odwrócić kolejność porównania, aby kompilator wykrył błąd:
   ```c
   if (6 == x) // jeśli użyjemy = to dostaniemy błąd, bo 6 nie jest lvalue
   ```
- Można też kompilować z flagami, które generują więcej warningów, przykładowo użycie ```-Wall``` w GCC daje nam:

   ```text
   main.c:6:9: warning: suggest parentheses around assignment used as truth value
   ```

---

## Błędy przy używaniu ```scanf()```

Istnieją dwa często spotykane błędy związane z użyciem funkcji ```scanf()```:

### Brak operatora adresu (```&```)

- ```scanf()``` wymaga adresu zmiennej, aby móc zapisać do niej dane.
- Oznacza to, że w przypadku większości typów należy użyć operatora ```&```, aby przekazać adres.

### Niewłaściwy format dla operandu

- Jeśli format podany w ```scanf()``` nie pasuje do typu zmiennej, wynik może być nieprzewidywalny.
    
---

## Rozmiar tablic w C

Tablice w języku C zawsze zaczynają się od indeksu 0. Oznacza to, że tablica składająca się z 10 elementów, zadeklarowana jako:

```c
int a[10];
```

będzie miała prawidłowe indeksy od 0 do 9, a nie do 10!

- Zawsze sprawdzaj, czy indeksy używane w tablicach mieszczą się w ich zakresie.

- Pamiętaj, że jeśli tablica ma ```n``` elementów, to ostatni element znajduje się pod indeksem ```n - 1```.

---

## Sprawdzanie rozmiaru tablic przed i po przekazaniu do funkcji


Z wykładu 4. wiemy, że:

- *Identyfikator tablicy w wywołaniu automatycznie przekształca się w wskaźnik do jej pierwszego elementu (array decays into pointer).*

- *W funkcji otrzymujemy wskaźnik do pierwszego elementu przekazanej tablicy.*

oraz, że operator ```sizeof```:

- *w przypadku tablic zwraca rozmiar całej tablicy w bajtach.*

---

## Sprawdzanie rozmiaru tablic i rozmiaru tablic przekazanych do funkcji

Oznacza to, że jeśli: 

- zadeklarujemy tablicę, np. ```int tab[10]``` i użyjemy ```sizof(tab)```, dostaniemy rozmiar całej tablicy w bajtach (najprawdopodobniej 40),

- jeśli mamy funkcję, która przyjmuje tablicę typu ```int``` i przekażemy do niej  tablicę ```tab```, to: *"Identyfikator tablicy w wywołaniu automatycznie przekształci się w wskaźnik".*

- jeśli w funkcji sprawdzimy rozmiar tego już wskaźnika, to dostaniemy rozmiar *wskaźnika*, nie tablicy!

---

## Przykład:

```c
#include <stdio.h>

void printSize(int *arr) {
    printf("Rozmiar w funkcji: %zuB\n", sizeof(arr)); 
}

int main() {
    int tab[10];
    printf("Rozmiar tab: %zuB\n", sizeof(tab)); 
    printSize(tab);
    return 0;
}
```
```
Rozmiar tab: 40B
Rozmiar w funkcji: 8B
```

---

## Dzielenie całkowite

Język C używa operatora ```/``` zarówno do dzielenia rzeczywistego, jak i całkowitego. 

Jeśli oba operandy są typu całkowitoliczbowego, zostanie użyte dzielenie całkowite. W przeciwnym razie zostanie wykonane dzielenie rzeczywiste.

```c
double half = 1 / 2;  // wynikiem będzie 0.0, nie 0.5!
```
Poprawnie:
```c
double half = 1.0 / 2; // wynikiem jest 0.5
```

Jeśli oba operandy są zmiennymi całkowitoliczbowymi, a chcemy wykonać dzielenie rzeczywiste, należy rzutować jedną ze zmiennych na typ ```double``` lub ```float```.

---

## Porównywanie łańcuchów znaków za pomocą ```==```

Nigdy nie używamy operatora ```==``` do porównywania wartości łańcuchów znaków!

Łańcuchy w C są tablicami typu ```char```, a nazwa tablicy działa jak wskaźnik do jej pierwszego elementu. Dlatego operator ```==``` porównuje adresy w pamięci, a nie zawartość łańcuchów.

---

## Porównywanie łańcuchów znaków za pomocą ```==```

```c
char st1[] = "abc";
char st2[] = "abc";

if(st1 == st2)
    printf("Yes");
else
    printf("No");
```

```
No
```

Aby porównać zawartość łańcuchów znaków, należy użyć funkcji ```strcmp``` z biblioteki ```<string.h>```.

---

## Niepozostawienie miejsca na znak końca ciągu (null terminator)

Ciąg znaków w języku C musi zawierać znak końca ciągu (```\0```) na końcu danych tekstowych. Częstym błędem jest niezarezerwowanie miejsca na ten dodatkowy znak.

W deklaracji ```char str[30];``` tablica może przechowywać maksymalnie 29 znaków danych (a nie 30), ponieważ jeden z miejsc musi być zarezerwowany dla ```\0```.

### Dlaczego to ważne?

Brak miejsca na znak końca ciągu może prowadzić do błędów odczytu nieokreślonych danych spoza zakresu tablicy (tzw. *buffer overrun*).
    
Funkcje operujące na ciągach (```strlen```, ```printf```) polegają na obecności znaku ```\0```. 

---

## Błędy w pętlach

W języku C pętla powtarza następującą po niej instrukcję: 

```c
int x = 5;
while (x > 0);
    x--;
```

Ten kod powoduje nieskończoną pętlę - średnik po ```while``` to pusta instrukcja, którą pętla ma powtarzać. W efekcie ciało pętli nie wykonuje żadnych działań.

### Inne typowe błędy w pętlach:

- **Za dużo lub za mało iteracji** - niewłaściwe warunki pętli mogą prowadzić do nieoczekiwanej liczby powtórzeń.

- **Brak zmian zmiennej sterującej** - może prowadzić do nieskończonych pętli.

---

<!--
<style scoped> li {font-size: 20pt} </style>
-->

# Bibliografia

1. Stephen Prata. 2013. C Primer Plus (6th Edition) (6th. ed.). Addison-Wesley Professional.
2. Stephen G. Kochan. 2014. Programming in C (4th. ed.). Addison-Wesley Professional.
3. Randal E. Bryant and David R. O'Hallaron. 2010. Computer Systems: A Programmer's Perspective (2nd. ed.). Addison-Wesley Publishing Company.
4. https://en.cppreference.com/w/c
5. Standard języka C (ISO/IEC 9899:TC3)
6. Lecture 10. CS107: Computer Organization & Systems, Stanford University, https://web.stanford.edu/class/cs107/
7. http://pacman128.github.io/internal/common_c_errors/

---

<!-- backgroundColor: '#eaeaea' -->
<!-- _footer: &nbsp; -->

# Dziękuję za uwagę!
