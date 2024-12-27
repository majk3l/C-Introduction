<!--
---
marp: true
footer: "| PwJC | Wykład #1 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/"
_footer: "| e-mail: mkepski@ur.edu.pl |"
paginate: true
_paginate: false
---
-->

# programowanie w języku C
### Wykład #1: Wprowadzenie.
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | 
-->

---
<!-- footer: | PwJC | Wykład #1 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/
     page_number: true -->


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
## Czy warto współcześnie uczyć się C?

- Opracowany w 1972 roku.
- Nie jest obiektowy (jak większość współczesnych języków).
- Niezbyt wygodne zarządzanie pamięcią.
---

## Czy warto współcześnie uczyć się C? Tak, bo:

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
- Struktura programu;
- Kompilacja i uruchomienie;

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
```c
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
- #### Plik z kodem źródłowym zawiera sekwencję bajtów.
&nbsp;
- #### Każdy z nich reprezentuje jeden znak w kodzie ASCII.
#### &nbsp;
---

## Informacja to bity + kontekst

```c
#include <stdio.h>

int main() {
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

- Wszystkie informacje w systemie komputerowym przechowywane są za pomocą bitów. 

- To co odróżnia poszczególne dane od siebie, to kontekst w jakim na nie patrzymy. 

- Jako programiści musimy odróżniać sprzętowe reprezentacje liczb, gdyż są one różne dla różnych ich typów.

---
## Programy są tłumaczone przez inne programy do różnych form

- Program, w postaci kodu źródłowego napisanego przez programistę jest tłumaczony do niskopoziomowej postaci - języka maszynowego.

- Język maszynowy to zestaw instrukcji dla procesora.

- Program ten następnie łączony jest z wymaganymi bibliotekami i przechowywany na dysku w formie binarnej (plik wykonywalny).

---
## Programy są tłumaczone przez inne programy do różnych form

### Język maszynowy
```text
00: 0005   5
01: 0008   8

10: 8A00   R[A] <- mem[00]
11: 8B01   R[B] <- mem[01]
12: 1CAB   R[C] <- R[A] + R[B]
13: 9C02   mem[02] <- R[C]
14: 0000   halt
```

Dodawanie w abstrakcyjnym języku maszynowym TOY (Princeton University).

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

# Podstawowe pojęcia: typ

Typ danych jest to zestaw atrybutów (cech) opisujących dane. Zazwyczaj dotyczy:

- rozmiaru,
- struktury,
- zakresu wartości,
- zbioru dozwolonych operacji.

Przykładowy typ danych w C to **typ całkowity** ```int```.

---

# Podstawowe pojęcia: typ ```int```

Przykładowy typ danych ```int``` na typowym 32-bitowym procesorze ma:

- **rozmiar**: 4 bajty,
- **zakresu wartości**: od -2147483648 do 2147483647,
- **zbiór dozwolonych operacji**:
  - unarnych: ```+```, ```-```, ```~```, ```&```, ```++```, ```--```,
  - binarnych: ```=```, ```+```, ```-```, ```*```, ```/```, ```%```, ```<```, ```>```, ```==```, ```!=```, ```&```, ```|```, ```&&```, ```||```.

---

# Podstawowe pojęcia

- **instrukcja** - najmniejszy element programu, zawiera akcję do wykonania (np. przypisanie wartości do zmiennej, wywołanie funkcji). 

- **program** - zestaw instrukcji wykonujący określone zadanie.

- **proces** - program komputerowy uruchomiony w systemie operacyjnym.

- **język programowania** - formalnie zdefiniowany zbiór reguł dotyczący semantyki i syntaktyki (składni) określający jak pisać programy.

---

## Języki programowania - podział

### Ze względu na:
- paradygmat,
- sposób wykonywania,
- kontrolę typów,
- poziom.

###

---

## Języki programowania - podział

### Ze względu na paradygmat:

 - **imperatywne**
  *program jako sekwencja instrukcji zmieniających stan systemu*;
  
  - **deklaratywne**
  *program opisuje jakie warunki ma spełniać końcowe rozwiązanie*;
  
---
## Języki programowania - podział

### Ze względu na paradygmat:

 - **proceduralne**
 *program podzielony na procedury, wykonujące określone operacje*;

 - **obiektowe**:
*program jako zbiór obiektów (łączących dane i zachowanie)*;

---

## Języki programowania - podział
### Ze względu na sposób wykonywania:

 - **kompilowane**
   *kod źródłowy programu zamieniany na kod wykonywalny przed uruchomieniem programu*;
 - **interpretowane**
   *programy wykonywane bezpośrednio przez interpreter bez konieczności zamiany na kod maszynowy*;
 
---

## Języki programowania - podział
### Ze względu na typowanie

- **statyczne**
  *typ zmiennych w programie jest znany w momencie kompilacji*;

- **dynamiczne**
  *typ jest określany w momencie wykonywania programu*;
  

---
## Języki programowania - podział
### Ze względu na poziom

- **wysokopoziomowe**
  *bardziej abstrakcyjne i oddzielają programistę od szczegółów sprzętowych*;

- **niskopoziomowe**
  *pozwalają na bezpośrednią kontrolę nad pamięcią i innymi zasobami systemu, składają się z instrukcji, które procesor może wykonywać bezpośrednio*;

---

## C

### Imperatywny, proceduralny, kompilowany, statycznie typowany.

*Omawiamy specyfikację C99.*


---

## Pisanie programów

1. Zdefiniuj cele dla programu.
2. Zaprojektuj program.
3. Napisz kod źródłowy.
4. Skompiluj.
5. Uruchom.
6. Testuj i debuguj.
7. Utrzymuj i zmieniaj według potrzeb kod źródłowy.

---

## Przykładowy program - struktura



```c
#include <stdio.h>

int main(){
    int num;
    num = 1;
    /* to jest komentarz */
    printf("Hello world!\n");
    return 0;
}
```

---

## Struktura programu
  
```text                           
     +-----------+
+----|#include...|   <------ dyrektywy preprocesora
|    +-----------+ 
|
|    +-----------+   
+----|int main() |   <------ funkcja główna, zwykle wywołuje inne funkcje zdefiniowane
|    +-----------+           w programie i funkcje biblioteczne
|              | 
|              |         +------------+      
|              +---------| instrukcje |  funkcje składają się z instrukcji 
|                        +------------+                         
|    +-----------+              |                   
+----|funkcja a()|             ...      
|    +-----------+               
|              |         +------------+          
|              +---------| instrukcje |  funkcje zwykle zwracają wartość, która        
|                        +------------+  może użyta w miejscu ich wywołania           
|    +-----------+              |                                 
+----|funkcja b()|             ...                               
|    +-----------+                                                                           
```
&nbsp;

---

## Kompilowanie i uruchomienie

### Kompilator gcc (Linux)


```bash
$ gcc hello.c -o hello
$ ./hello
Hello world!
```

- wywołanie kompilatora gcc w ten sposób tworzy plik wykonywalny o nazwie ```hello```
- flaga ```-o``` umożliwia nadanie nazwy plikowi wynikowemu
- ```./``` przed nazwą programu (w lokalnym folderze) służy do uruchomienia go

---

## Inne flagi kompilatora

- ```-Wall``` – włącza ważne ostrzeżenia, ułatwia znalezienie błędów.
- ```-Werror``` – traktuje ostrzeżenia jako błędy.
- ```-g``` – dodaje informacje potrzebne do debugowania (np. w GDB).
- ```-O``` (lub ```-O2```, ```-O3```) – optymalizacja kodu.

---

## Kompilacja, uruchomienie, debugowanie - przykład

---

# Bibliografia

1. Stephen Prata. 2013. C Primer Plus (6th Edition) (6th. ed.). Addison-Wesley Professional.
2. https://en.cppreference.com/w/c
3. Standard języka C (ISO/IEC 9899:TC3): https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf 

---

<!-- backgroundColor: '#eaeaea' -->
<!-- _footer: &nbsp; -->

# Dziękuję za uwagę!
