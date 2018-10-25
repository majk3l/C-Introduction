
# Podstawy programowania w języku C
### Wykład #1: Wprowadzenie
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | 
-->

---
<!-- footer: | PPwJC | Wykład #1: Wprowadzenie | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/
     page_number: true -->


## Sprawy organizacyjne 


- 15 godzin wykładu
- konsultacje: czwartek, 14:45-16:00; 201B4
- sylabus: https://goo.gl/y5neNk
- materiały z wykładu dostępne na GitHubie prowadzącego:
https://github.com/majk3l/C-Introduction/
- dodatkowe zestawy ćwiczeń (dla chętnych) w repozytorium, katalog ```exercises```

---
## Czego dotyczy wykład?
&nbsp;
### Głównie programowania w języku C
&nbsp;
&nbsp;
&nbsp;

---
## Ale...

- główne elementy składowe występują w większości języków **imperatywnych** i działają podobnie,
- niektóre treści wykładu odnoszą się ogólnie do dobrych praktyk pisania programów,
- znajomość C jest przydatna przy dalszej nauce (C++, Java).
  
---
## Czy warto uczyć się C w 2018?

- Opracowany w 1972 roku.
- Nie jest obiektowy (jak większość współczesnych języków).
- Niezbyt wygodne zarządzanie pamięcią.
---

## Czy warto uczyć się C w 2018? Tak, bo:

- Pozwala bardziej zrozumieć mechanizmy działania komputera.
- Jest ciągle popularny w świecie IT (programowanie systemowe, systemy wbudowane, mikrokontrolery).
- Jego popularność nie spadnie (według czasopisma *IEEE Spectrum*).

---
<!-- footer: | PPwJC | Wykład #1: Wprowadzenie | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/
     page_number: true -->

## Plan wykładu:

- System komputerowy z punktu widzenia programisty;
- Podstawowe pojęcia;
- Języki programowania - podział;
- Pierwszy program w C;
- Struktura programu, typy, funkcje.

---

## System komputerowy z punktu widzenia programisty

---

## System komputerowy

To:
- sprzęt,
- oprogramowanie systemowe (system operacyjny, biblioteki),

które współdziałają w celu uruchamiania programów (aplikacji) użytkownika.

---
## Informacja to bity + kontekst

&nbsp;
```
#include <stdio.h>

int main()
{
    printf("hello, world\n");
}
```
---
## Informacja to bity + kontekst

### To jest kod źródłowy programu:
```c
#include <stdio.h>

int main()
{
    printf("hello, world\n");
}
```
programy napisane w języku C przechowujemy z rozszerzeniem ```.c```, np. ```hello.c``` 

---

## Informacja to bity + kontekst

#### &nbsp;
#### Plik z kodem źródłowym zawiera sekwencję bajtów.
#### &nbsp;
#### Każdy z nich reprezentuje jeden znak w kodzie ASCII.
#### &nbsp;
---

## Informacja to bity + kontekst

```c
#include <stdio.h>

int main()
{
    printf("hello, world\n");
}
```

```text
#   i   n  c   l   u   d   e  <sp>  <   s   t   d   i   o  .
35 105 110 99 108 117 100 101  32   60 115 116 100 105 111 46

 h  >  \n \n  i   n   t  <sp>  m  a   i   n  (  )  \n  {
104 62 10 10 105 110 116  32  109 97 105 110 40 41 10 123

\n  <sp><sp><sp><sp> p   r   i   n   t   f  (  "   h   e   l
10   32  32  32  32 112 114 105 110 116 102 40 34 104 101 108

 l   o  ,  <sp>  w   o   r   l   d  \   n  "  )  ;  \n  }
108 111 44  32  119 111 114 108 100 92 110 34 41 59 10 125
```
---
## Informacja to bity + kontekst

#### Wszystkie informacje w systemie komputerowym przechowywane są za pomocą bitów. 

#### To co odróżnia poszczególne dane od siebie, to kontekst w jakim na nie patrzymy. 

#### Jako programiści musimy odróżniać sprzętowe reprezentacje liczb, gdyż są one różne dla różnych ich typów.

---
## Programy są tłumaczone przez inne programy do różnych form

#### Program, w postaci kodu źródłowego napisanego przez programistę jest tłumaczony do niskopoziomowej postaci - języka maszynowego.

#### Język maszynowy to zestaw instrukcji dla procesora.

#### Program ten następnie łączony jest z wymaganymi bibliotekami i przechowywany na dysku w formie binarnej (plik wykonywalny).

---
## Programy są tłumaczone przez inne programy do różnych form

### Język maszynowy

&nbsp;
```text
00: 0005   5
01: 0008   8

10: 8A00   R[A] <- mem[00]
11: 8B01   R[B] <- mem[01]
12: 1CAB   R[C] <- R[A] + R[B]
13: 9C02   mem[02] <- R[C]
14: 0000   halt
```
&nbsp;
<sup><sup>Dodawanie w abstrakcyjnym języku maszynowym TOY (Princeton University)</sup></sup>

---

## Programy są tłumaczone przez inne programy do różnych form
### Asembler

Język asemblerowy to taki, którego instrukcje odpowiadają rozkazom procesora w kodzie maszynowym, ale ich liczbowa reprezentacja została zastąpiona przez mnemoniki. 

Jest bardziej czytelny dla człowieka niż język maszynowy.

---

## Programy są tłumaczone przez inne programy do różnych form
### Asembler

```asm
.LC0:
        .string "Hello, world!"
main:
        pushq   %rbp
        movq    %rsp, %rbp
        movl    $.LC0, %edi
        call    puts
        movl    $0, %eax
        popq    %rbp
        ret
  ```
---
## Programy są tłumaczone przez inne programy do różnych form


```sh  
hello.c      hello.i       hello.s        hello.o        hello 
    _________      ________      _________      ________
   |         |    |        |    |         |    |        |
   |   pre-  |    |compiler|    |assembler|    | linker |
-->|processor|--->| (ccl)  |--->|  (as)   |--->|  (ld)  |->
   |  (cpp)  |    |________|    |_________|    |________|
   |_________|    
```

```hello.c``` - program źródłowy (tekst)  
```hello.i``` - zmodyfikowany program źródłowy (tekst)  
```hello.s``` - program w asemblerze (tekst)  
```hello.o``` - relokowalny plik obiektowy (binarny)  
```hello```  &nbsp;&nbsp;&nbsp; - plik wykonywalny (binarny) 

---

## Podstawowe pojęcia

---

## Podstawowe pojęcia

- **program** - zestaw instrukcji wykonujący określone zadanie

- **proces** - program komputerowy uruchomiony w systemie operacyjnym

- **język programowania** - formalnie zdefiniowany zbiór reguł dotyczący semantyki i syntaktyki (składni) określający jak pisać programy

---

## Języki programowania - podział

### Ze względu na:
- paradygmat
- sposób wykonywania 
- kontrolę typów
- poziom

###
---

## Języki programowania - podział

### Ze względu na paradygmat

- imperatywne
&nbsp;
- deklaratywne

---
## Języki programowania - podział

### Ze względu na paradygmat

 - imperatywne
  *program jako sekwencja instrukcji zmieniających stan systemu*
  
  - deklaratywne
  *opisują jakie warunki ma spełniać końcowe rozwiązanie*
  
---
## Języki programowania - podział

### Ze względu na paradygmat

 - proceduralne
&nbsp;
 - obiektowe

---
## Języki programowania - podział
### Ze względu na sposób wykonywania

 - kompilowane
&nbsp;
 - interpretowane
---
## Języki programowania - podział
### Ze względu na typowanie

- statyczne
&nbsp;
- dynamiczne

---
## Języki programowania - podział
### Ze względu na poziom

- wysokopoziomowe
&nbsp;
- niskopoziomowe

---

## C

### Imperatywny, proceduralny, statycznie typowany
