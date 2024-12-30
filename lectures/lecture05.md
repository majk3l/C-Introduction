<!--
---
marp: true
footer: "| PwJC | Wykład #5 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/"
_footer: "| e-mail: mkepski@ur.edu.pl |"
paginate: true
_paginate: false
---
-->

# programowanie w języku C
### Wykład #5: Struktury, unie.
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | 
-->

---
<!-- footer: | PwJC | Wykład #5 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/
     page_number: true -->


# Struktury

- grupują dane w nowy, złożony typ,

- pojedyncza zmienna typu strukturalnego może zawierać wiele danych (liczb, tekst, wskaźniki itd.),

- musimy zdefiniować jak struktura będzie zbudowana, np:

   ```c
   struct point2i {
      int x;
      int y;
   };
   ```
- średnik po nawiasie jest wymagany.

---

## Struktury

- ```struct``` definiuje nowy typ danych:
   
   ```c
   struct point2i {
      int x;
      int y;
   };
   ```

- powyższe to tylko definicja typu: nie istnieją jeszcze żadne tego typu zmienne,

- zmienne typu strukturalnego mogą być deklarowane jak każde inne:

   ```c
   struct point2i startPoint; 
   ```

---

## Struktury

- Przy definiowaniu tego, jak będzie wyglądać struktura, możemy od razu utworzyć zmienną będącą taką strukturą:

   ```c
   struct point2i {
      int x;
      int y;
   } startPoint;
   ```

- ```startPoint``` jest strukturą typu ```point2i```.

---

## Struktury anonimowe

Tworzenie zmiennej **bez nadawania identyfikatora typowi**:

```c
struct {       // brak nadanej nazwy typu, po słowie 
   int x;      // kluczowy struct od razu występuje {
   int y;
} startPoint;
```

```startPoint``` jest zmienną i możemy jej używać, ale nie nazywając typu przy definicji pozbawiamy się wygodnego sposobu na deklarowanie kolejnych takich struktur w kolejnych liniach kodu programu. 

---

## Struktury

- przy tworzeniu zmiennych  musimy używać składni ```struct nazwa_typu``` :

   ```c
   struct point2i startPoint; 
   ```
- możemy tego uniknąć, gdy przy definiowaniu struktury użyjemy słowa kluczowego ```typedef```:

   ```c
   typedef struct point2i {
      int x;
      int y;
   } point2i;
   ```
- dzięki temu zmienną tworzymy tak:
   ```c
   point2i startPoint; 
   ```

---

## dlaczego to działa? słowo o ```typedef```

Jak działa poniższy fragment kodu? I dlaczego ```point2i``` występuje tam dwukrotnie?

```c
typedef struct point2i {
    int x;
    int y;
} point2i;
```

```typedef``` jest używany do tworzenia skróconej nazwy dla istniejącego typu w języku C:

```c
typedef istniejacy_typ nowy_typ;
```

---

## dlaczego to działa? słowo o ```typedef```

```typedef``` możemy często znaleźć w plikach nagłówkowych, gdzie tworzone są różne aliasy dla typów. Przykład z pliku ```_types.h``` dla kompilatora C pod architekturę ARM (Apple): 

```c
#ifndef _BSD_ARM__TYPES_H_
#define _BSD_ARM__TYPES_H_

#if defined (__arm__) || defined (__arm64__)

#ifdef __GNUC__
typedef __signed char           __int8_t;
#else   /* !__GNUC__ */
typedef char                    __int8_t;
#endif  /* !__GNUC__ */
typedef unsigned char           __uint8_t;
typedef short                   __int16_t;
```

---

## dlaczego to działa? słowo o ```typedef```

```c
typedef istniejacy_typ nowy_typ;
```

Analogiczne używając ```typedef``` przy definiowaniu struktury:

```c
typedef struct point2i {
    int x;
    int y;
} point2i;
```

```point2i``` staje się aliasem dla całej definicji struktury.

---

## Struktury jako składowe 

Składowymi struktur mogą być inne struktury, co pozwala na dodatkowe grupowanie danych w logiczne jednostki:

```c
struct triangle {
   struct point2i ptA;
   struct point2i ptB;
   struct point2i ptC;
};
```

Definicja typu zawieranego (w tym wypadku ```point2i```) musi znajdować się przed definicją struktury zawierającej składowe o tym typie danych.

---

## Struktury - inicjalizacja

```c
struct point2i a = {0,0};  //  pojedynczej struktury 
struct point2i b = {1,1};
struct point2i c = {0,2};

// zagnieżdżona struktura:
struct triangle abc = {a, b, c};              // za pomocą struktur
struct triangle efg = {{1,1}, {2, 1}, {2,2}}; // za pomocą wartości
struct triangle afg = {a, {2, 1}, {2,2}};     // sposób mieszany 
```

---


## Struktury - inicjalizacja

Tak nie robimy:

```c
struct point 
{ 
  int x = 0; //COMPILER ERROR: can't initialize members
  int y = 0; //COMPILER ERROR: can't initialize members
};  
```

---

## Struktury - dostęp do składowych

Jeśli mamy zmienną będącą strukturą, to:

- do elementów struktury dostajemy się operatorem ```.```.
- jeśli struktura jest zagnieżdżona, wiele ```.``` będzie konieczne.

Jeśli mamy wsaźnik do struktury, to:

- do elementów struktury dostajemy się operatorem ```->```.
- jeśli mamy wskaźnik do zagnieżdżonej struktury, to najprawdopodobniej będziemy potrzebować zarówno ```->``` i ```.```.

---

## Struktury - przykład (```05_struct_point_triangle.c```)

--- 

<!--
<style scoped> p {margin: 4px} pre {padding: 10px} </style>
-->

## Struktury - rozmiar

*The size of a structure is greater than or equal to the sum of the sizes of its members.*

```c
struct point2i {
    int x;
    int y;
};
struct triangle {
   struct point2i ptA;
   struct point2i ptB;
   struct point2i ptC;
};
printf("sizeof(int): %zu \n", sizeof(int));
printf("sizeof(point2i): %zu \n", sizeof(struct point2i));
printf("sizeof(triangle): %zu \n", sizeof(struct triangle));
```

```text
sizeof(int): 4
sizeof(point2i): 8 
sizeof(triangle): 24 
```

--- 

<!--
<style scoped> 
h2{margin-bottom: 0px;} 
pre {padding: 10px;} code {font-size: 16pt;} 
p {margin: 6px; font-size: 22pt;} pre {padding: 10px;} 
</style>
-->

## Struktury - rozmiar

 Kompilator może uzupełniać (*padding*) mniejsze typy do stałej szerokości (np. 4 bajty). Zależy to od reguł wyrównania (*alignment*) używanych przez daną platformę i kompilator. 

```c
struct padded_structure {
    unsigned short s; 
    int   i; 
    char  c; 
};
printf("sizeof(unsigned short): %zu \n", sizeof(unsigned short));
printf("sizeof(int): %zu \n", sizeof(int));
printf("sizeof(char): %zu \n", sizeof(char));
printf("sizeof(padded_structure): %zu \n", sizeof(struct padded_structure));
```
```text
sizeof(unsigned short): 2 
sizeof(int): 4 
sizeof(char): 1 
sizeof(padded_structure): 12 
```

---

## Struktury jako parametr funkcji

Struktury mogą być argumentami oraz typami zwracanymi funkcji.

Do funkcji (struktura jako parametr) mogą być przekazywane przez wartość bądź przez wskaźnik, z funkcji (typ zwracany funkcji) mogą być zwracane również jako wartości lub wskaźniki. 

---

## Struktury jako parametr funkcji

### Przekazywanie przez wartość:

- kopia całej struktury jest tworzona i przekazywana do funkcji,
- zmiany w strukturze wewnątrz funkcji nie wpływają na oryginalną strukturę,
- może być kosztowne w przypadku dużych struktur.

```c
void printPoint(struct point2i p) {
    printf("Point: (%d, %d)\n", p.x, p.y);
}
```

---

## Struktury jako parametr funkcji


### Przekazywanie przez wskaźnik

- dla dużych struktur warto rozważyć użycie wskaźnika, przy przekazywaniu ich do funkcji,

- funkcja działa na oryginalnej strukturze, więc zmiany dokonane funkcji wpływają na oryginalną strukturę.

```c
void movePoint(struct Point *p, int dx, int dy) {
    p->x += dx;
    p->y += dy;
}
```

---

<!--
<style scoped> 
h2{margin-bottom: 0px;} 
pre {padding: 10px;} 
</style>
-->

## Struktury - dynamiczna alokacja pamięci

```c
#include <stdio.h>
#include <stdlib.h>

struct point2i {
    int x;
    int y;
} startPoint;

int main() {
    struct point2i *p = (struct point2i *)malloc(sizeof(struct point2i)); 
    if (p == NULL) {
        printf("Error allocating memory!\n");
        return 1;
    }
    p->x = 5;
    p->y = 10;
    printf("Point: (%d, %d)\n", p->x, p->y);
    free(p); 
    return 0;
}
```

---

## Struktury - wykorzystanie

### przykład: lista wskaźnikowa

```c
 struct element {
   struct element *next; // wskaźnik na kolejny element
   int val;              // przechowywana wartość 
};
```

---

## Struktury - przykład (```05_struct_linked_list.c```)

---

## Tablice struktur

- Tablice struktur umożliwiają przechowywanie wielu elementów tego samego typu struktury w jednym zbiorze.

- Ułatwiają pracę z danymi o podobnej strukturze, np. listą punktów, uczniów, pracowników itp.

- Mogą być zadeklarowane jako tablice o stałym rozmiarze lub dynamicznie alokowane.

---

## Tablice struktur

```c
#include <stdio.h>

struct point2i {
    int x;
    int y;
};

int main() {
    struct point2i points[3] = {
        {0, 0},  // Punkt 1
        {1, 1},  // Punkt 2
        {2, 2}   // Punkt 3
    };
    for (int i = 0; i < 3; i++) {
        printf("Punkt %d: (%d, %d)\n", i + 1, points[i].x, points[i].y);
    }
    return 0;
}
```

---

<!--
<style scoped> h2 {font-size: 27pt} </style>
-->

## Dynamiczna alokacja - przykład (```05_dynamic_struct_array.c```)

---

## Tablice jako elementy struktur

- W strukturach można definiować tablice jako pola.

- Pozwala to na przechowywanie zestawów powiązanych danych w jednym obiekcie, np. przechowywanie punktów, wektorów czy danych użytkownika.

---

## Tablice jako elementy - przykład (```05_struct_with_array.c```)

---

# Unie

Unia jest typem złożonym, który może przechowywać wiele typów danych w jednej lokalizacji pamięci. 

---
## Unie

Jednak pola unii nakładają się na siebie, w danej chwili można w niej przechowywać wartość tylko jednego typu. Przykład: 

```c
union data {
    int idata;
    float fdata;
    char* sdata;
} d1; 

d1.idata = 10;
d1.fdata = 3.14F;
d1.sdata = "Hello world"; 
```

---
## Unie

Miejsce na unię jest tak zwymiarowane przez kompilator, aby mogło pomieścić największą składową. 

Umożliwia to oszczędne gospodarowanie pamięcią i jednocześnie manipulowanie danymi różnego rodzaju w tym samym miejscu pamięci.

Korzystać z unii należy umiejętnie, gdyż łatwo nadpisać wartości, a próba odczytu nadpisanego pola może prowadzić do błędów.

---

# Bibliografia

1. Stephen Prata. 2013. C Primer Plus (6th Edition) (6th. ed.). Addison-Wesley Professional.
2. Stephen G. Kochan. 2014. Programming in C (4th. ed.). Addison-Wesley Professional.
3. https://en.cppreference.com/w/c
4. Standard języka C (ISO/IEC 9899:TC3): https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf 

---

<!-- backgroundColor: '#eaeaea' -->
<!-- _footer: &nbsp; -->

# Dziękuję za uwagę!
