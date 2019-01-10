
# Podstawy programowania w języku C
### Wykład #4: Tablice, wskaźniki, alokacja pamięci
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | 
-->

---
<!-- footer: | PPwJC | Wykład #2 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/
     page_number: true -->

# Tablice

- Złożone z wielu elementów tego samego typu.
- Deklaracja tablicy informuje ile elementów będzie zawierała tablica (ile zarezerwować pamięci).
- dostęp do poszczególnych elementów przez operator indeksowania ```[]```, poczynając od 0.


---
# Tablice

#### Przykładowe deklaracje tablic

```c
float data_per_day[365]; /* tablica 365 float-ów */
char name[12];           /* tablica 12 char-ów */
int indices[50];         /* tablica 50 int-ów */
```
---
# Inicjalizacja tablic

Inicjalizacja zwykłej zmiennej: 

```
int instances = 1;
```
Inicjalizacja tablicy:

```
int powers[8] = {1,2,4,6,8,16,32,64};
```

---
# Tablice 

- Można używać ```const```, ale łączymy go z inicjalizacją.
- Jeśli nie zainicjalizujemy tablicy, to jej elementy będą zawierać dane przechowywane w pamięci dotychczas pod tymi adresami. **Pamiętamy o inicjalizacji tablic!**

---
# Inicjalizacja w C99

#### inicjalizacja klasyczna (ANSI C)

```c
int arr[6] = {0, 0, 0, 0, 0, 123}; // ANSI C
```

#### tzw. *designated initializers*

```c
int arr[6] = {[5] = 123}; // ustawia arr[5] na wartość 123
```
<sub>pozostałe elementy otrzymują wartość 0.</sub>

---
# Inicjalizacja w C99

```c
// designate.c -- use designated initializers

#include <stdio.h>

#define MONTHS 12

int main(void)
{
    int days[MONTHS] = {31,28, [4] = 31,30,31, [1] = 29};
    int i;
    for (i = 0; i < MONTHS; i++) {
        printf("%2d %d\n", i + 1, days[i]);
   }
   return 0;
}
```
<sup><sup> Źródło: Prata S.: *C Primer Plus*, 6th ed., Addison-Wesley, 2014 </sup></sup>


---
# Deklaracja rozmiaru

```c

int n = 5;
int m = 8;
float a1[5]; // yes
float a2[5*2 + 1]; // yes
float a3[sizeof(int) + 1]; // yes
float a4[-4]; // no, size must be > 0
float a5[0]; // no, size must be > 0
float a6[2.5]; // no, size must be an integer
float a7[(int)2.5]; // yes, typecast float to int constant
float a8[n]; // not allowed before C99
float a9[m]; // not allowed before C99

```
---
# Tablice wielowymiarowe

- przydatne przy reprezentacji wielowymiarowych danych, np. obrazów.

przykładowa deklaracja:
```
float t[5][12]; // array of 5 arrays of 12 floats
```
---
# Tablice wielowymiarowe

```c
float t[5][12];
```

```text
--------------------12-------------------------->
|   _ _ _ _     _ _ _ _     _ _ _ _     _ _ _ _  kazda
|  | | | | |   | | | | |   | | | | |   | | | | | komórka
|  +-------+   +-------+   +-------+   +-------+ 4 bajty
|  |t[0][0]| - |t[0][1]| - |t[0][2]| - |t[0][3]|  ...
|  +-------+   +-------+   +-------+   +-------+
|      |           |           |           |
|  +-------+   +-------+   +-------+   +-------+
5  |t[1][0]| - |t[1][1]| - |t[1][2]| - |t[1][3]|  ...
|  +-------+   +-------+   +-------+   +-------+
|      |           |           |           |
|  +-------+   +-------+   +-------+   +-------+
|  |t[2][0]| - |t[2][1]| - |t[2][2]| - |t[2][3]|  ...
|  +-------+   +-------+   +-------+   +-------+
v     ...         ...         ...         ...    
```

---
# Tablice wielowymiarowe - inicjalizacja 

```c
#include <stdio.h>
#define MONTHS 12 // liczba miesięcy
#define YEARS 4 // liczba lat

int main(void)
{
    // inicjalizacja (4 x 12) elementów tablicy
    const float data[YEARS][MONTHS] =
    {
        {2.3,4.3,4.3,3.2,1.0,1.5,0.8,0.2,0.4,2.4,3.5,6.6},
        {7.5,8.2,1.2,1.6,2.4,0.0,5.2,0.7,0.1,0.8,1.2,7.1},
        {8.6,7.5,6.7,9.9,2.1,0.6,0.3,0.6,1.1,2.3,6.1,8.3},
        {7.2,9.9,8.4,3.3,1.2,5.8,0.4,0.0,0.6,2.7,4.3,6.1}
    };
    /// ...
    return 0;
}
```

---
# Tablice wielowymiarowe - dostęp do elementów

```c
for (year = 0, total = 0; year < YEARS; year++){
    // przetwarzaj każdy rok
    for (month = 0, subtot = 0; month < MONTHS; month++){
        ... // przetwarzaj każdy miesiąc
    }
    ... // przetwarzaj każdy rok
}

```
---

# Łańcuchy tekstowe

- Reprezentowane jako tablice elementów typu ```char```
- C nie narzuca ograniczeń długości łańcucha tekstowego.

---
# Łańcuchy tekstowe a typ ```char```

&nbsp; 

```text
                       +---+
'x' (znak)     ---->   | x |
                       +---+
                       +---+----+
"x" (łańcuch)  ---->   | x | \0 |
                       +---+----+
                            znak końca łańcucha
```

<sup><sup> Źródło: Prata S.: *C Primer Plus*, 6th ed., Addison-Wesley, 2014 </sup></sup>

---

# Wskaźniki

- Zmienna która wskazuje na inną zmieną, czyli przechowuje jej adres w pamięci.
- Adres może posłużyć do dostępu czy modyfikacji zmiennej z dowolnego miejsca programu.
- Bardzo użyteczne, szczególnie przy wykorzystywaniu struktur danych.

---

# Pamięć

Pamięć fizyczna:

- rejestry procesora
- cache
- RAM
- dysk twardy 
- removable storage

Pamięć wirtualna: abstrakcja dostarczona przez OS, możliwa do zaadresowania w kodzie źródłowym. 

---

# Wskaźniki 

- Każda zmienna przechowywana w pamięci ma swój adres
- Co nie ma adresu?
  - zmienne zadeklarowane jako ```register```
  - stałe/literały/definicje preprocesora
  - wyrażenia (jeśli ich rezultatem nie jest zmienna

---
# Wskaźniki

Jak pobrać adres zmiennej? operator ```&```:


```c

int n = 4;
double pi = 3.14159;

int *pn = &n;        /* adres zmiennej typu int */
double *ppi = &pi;   /* adres zmiennej typu double */
```
• Address zmienntej typu ```t``` posiada typ ```t*```

---
# Dereferencja wskaźnika

## Accessing/modifying addressed variable:

***dereferencing/indirection operator ```*```***

```c
printf("pi = %g\n", *ppi);

*ppi = *ppi + *pn;

```

*null pointer* (0, NULL) jest wskaźnikiem który nie odnosi się do niczego.  

---
# Wskaźniki - rzutowanie


Możemy rzutować wskaźnik danego typu na wskaźnik dowolnego innego typu:


```c
ppi = (double*)pn; /* pn originally of type (int*) */ 
```

---

# Wskaźniki - wykorzystanie


## #1 Funkcje zwracające więcej niż jedną wartość


```c
void swap (int* x, int* y) {
    int temp = *x;
    *x = *y;
    *y = temp;
}
```

---

# Wskaźniki - wykorzystanie


## #2 Przekazywanie tablic do funkcji

```c
void myFunction(int *param, int size) {
   .
   .
   .
}
```

---
