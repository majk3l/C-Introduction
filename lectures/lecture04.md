<!--
---
marp: true
footer: "| PwJC | Wykład #4 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/"
_footer: "| e-mail: mkepski@ur.edu.pl |"
paginate: true
_paginate: false
---
-->

# programowanie w języku C
### Wykład #4: Tablice, wskaźniki, alokacja pamięci.
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | 
-->

---
<!-- footer: | PwJC | Wykład #4 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/
     page_number: true -->

# Tablice

- Złożone z wielu elementów tego samego typu.
- Deklaracja tablicy informuje ile elementów będzie zawierała tablica (ile zarezerwować pamięci).
- Dostęp do poszczególnych elementów przez operator indeksowania ```[]```, poczynając od 0.


---
## Tablice

#### Przykładowe deklaracje tablic

```c
float data_per_day[365]; /* tablica 365 float-ów */
char name[12];           /* tablica 12 char-ów */
int indices[50];         /* tablica 50 int-ów */
```
---
## Inicjalizacja tablic

Inicjalizacja zwykłej zmiennej: 

```c
int instances = 1;
```
Inicjalizacja tablicy:

```c
int powers[8] = {1,2,4,6,8,16,32,64};
```

---
## Tablice 

- Można używać ```const```, ale łączymy go z inicjalizacją.

- Jeśli nie zainicjalizujemy tablicy, to jej elementy będą zawierać dane przechowywane w pamięci dotychczas pod tymi adresami. **Pamiętamy o inicjalizacji tablic!**

---

## Przykład indeksowania

```c
#include <stdio.h>

int main(void) {
    int tab[5];         
    int i;
    // Zapis do tablicy
    for (i = 0; i < 5; i++) {
        tab[i] = i * 10;  
    }
    // Odczyt z tablicy
    for (i = 0; i < 5; i++) {
        printf("tab[%d] = %d\n", i, tab[i]);
    }

    return 0;
}
```

---

## operator ```sizeof```

W C operator ```sizeof``` zwraca rozmiar operandu w bajtach, przy czym operand może być zarówno identyfikatorem, jak i nazwą typu danych.

W przypadku tablic zwraca rozmiar całej tablicy w bajtach.

Operator ```sizeof``` zwraca wartość typu ```size_t```, która jest typem całkowitym bez znaku.

---

## operator ```sizeof```

```c
#include <stdio.h>

int main(void) {

    int tab[10];
    printf("Rozmiar tab: %zu bajtów\n", sizeof(tab)); 
    printf("Liczba elementów: %zu\n", sizeof(tab) / sizeof(tab[0]));

   return 0;
}
```

```text
Rozmiar tab: 40 bajtów
Liczba elementów: 10
```

---

## Inicjalizacja w C99

#### inicjalizacja klasyczna (ANSI C)

```c
int arr[6] = {0, 0, 0, 0, 0, 123}; // ANSI C
```

#### tzw. *designated initializers*

```c
int arr[6] = {[5] = 123}; // ustawia arr[5] na wartość 123, pozostałe na 0
```

---

## Inicjalizacja w C99

```c
// Źródło: Prata S.: *C Primer Plus*, 6th ed., Addison-Wesley, 2014
// designate.c -- use designated initializers

#include <stdio.h>

#define MONTHS 12

int main(void) {
    int days[MONTHS] = {31,28, [4] = 31,30,31, [1] = 29};
    int i;
    for (i = 0; i < MONTHS; i++) {
        printf("%d ", days[i]);
   }
   return 0;
}
```
```text
31 29 0 0 31 30 31 0 0 0 0 0 
```

---

## Deklaracja rozmiaru

```c
int n = 5;
int m = 8;
float a1[5];        // poprawne
float a2[5*2 + 1];  // poprawne
float a3[sizeof(int) + 1]; // poprawne
float a4[-4];  // błąd, wymagany jest rozmiar > 0
float a5[0];   // błąd, wymagany jest rozmiar > 0
float a6[2.5]; // błąd, rozmiar musi być liczbą całkowitą
float a7[(int)2.5]; // poprawne, rozmiar zostanie rzutowany do typu int
float a8[n];   // niedozwolone przed C99
float a9[m];   // niedozwolone przed C99
```
---

## Tablice wielowymiarowe

- przydatne przy reprezentacji wielowymiarowych danych, np. obrazów.

- przykładowa deklaracja:
```c
float t[5][12]; // tablica pięciu tablic 12-elementowych, typu float
```
---
## Tablice wielowymiarowe

<!--
<style scoped> h2 {margin-bottom: 0px;} p {margin-bottom: 5px}</style>
-->

```c
float t[5][12];
```
Można wyobrazić sobie ```t``` jako tablicę dwuwymiarową złożoną z 5 wierszy i 12 kolumn:

```text
+-----------------------12---------------------->
| +-------+   +-------+   +-------+   +-------+         każda komórka 
| |t[0][0]| - |t[0][1]| - |t[0][2]| - |t[0][3]|  ...      to 4 bajty
| +-------+   +-------+   +-------+   +-------+
5     |           |           |           |
| +-------+   +-------+   +-------+   +-------+
| |t[1][0]| - |t[1][1]| - |t[1][2]| - |t[1][3]|  ...
| +-------+   +-------+   +-------+   +-------+
|     |           |           |           |
v    ...         ...         ...         ...    
```

Widok 2D jest jedynie wygodnym sposobem wizualizacji. Wewnątrz pamięci tablica jest przechowywana sekwencyjnie.

&nbsp;

---

## Tablice wielowymiarowe - inicjalizacja 

```c
#include <stdio.h>
#define MONTHS 12 // liczba miesięcy
#define YEARS 4   // liczba lat

int main(void) {
    // inicjalizacja (4 x 12) elementów tablicy
    const float data[YEARS][MONTHS] = {
        {2.31,4.3,4.3,3.2,11.0,1.5,0.8,0.2,0.4,2.4,3.5,6.65},
        {7.5,8.2,1.2,11.6,2.4,0.0,5.21,0.7,0.1,0.85,1.2,7.12},
        {8.61,7.5,6.7,9.94,2.1,0.65,0.315,0.68,1.1,2.3,6.1,8.38},
        {7.26,9.9,8.4,3.3,1.2,5.8,0.4,0.0,0.6,12.7,4.3,6.17}
    };
    /// ...
    return 0;
}
```

---

## Tablice wielowymiarowe - dostęp do elementów

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

- Reprezentowane jako tablice elementów typu ```char```.

- C nie narzuca ograniczeń długości łańcucha tekstowego.

- Biblioteka standardowa dostarcza zestawu funkcji do operowania na łańcuchach tekstowych, jak sprawdzanie długości, porównywanie, kopiowanie itp.

---

## Łańcuchy tekstowe, a typ ```char```

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

---

# Wskaźniki

- Zmienna która wskazuje na inną zmieną, czyli przechowuje jej adres w pamięci.
- Adres może posłużyć do dostępu czy modyfikacji zmiennej z dowolnego miejsca programu.
- Bardzo użyteczne, szczególnie przy wykorzystywaniu struktur danych.

---

## Pamięć

Pamięć fizyczna:

- rejestry procesora,
- cache,
- RAM,
- dysk twardy, 
- removable storage.

Pamięć wirtualna: abstrakcja dostarczona przez OS, możliwa do zaadresowania w kodzie źródłowym. 

---

## Wskaźniki 

- Każda zmienna przechowywana w pamięci ma swój adres.
- Co nie ma adresu?:
  - zmienne zadeklarowane jako ```register```,
  - stałe/literały/definicje preprocesora,
  - wyrażenia (jeśli ich rezultatem nie jest zmienna).

- *Null pointer* (```0```, ```NULL```) jest wskaźnikiem, który nie odnosi się do niczego. 

---

## Obiekty, ```lvalue``` i ```rvalue```

W języku C termin obiekt (*object*) nie odnosi się do obiektów znanych z programowania obiektowego (jak np. w C++, Javie czy Pythonie).

 Obiekt w C to obszar pamięci (region pamięci), w którym można przechowywać wartości.  Każda zmienna, każdy element struktury czy tablicy (jak np. ```tab[0]```) to w rozumieniu standardu C obiekty.

---

## Obiekty, ```lvalue``` i ```rvalue```

Obiekt to faktyczny obszar pamięci przechowujący dane. ```lvalue``` to nazwa lub wyrażenie (np. zmienna, element tablicy, pole struktury, wyrażenie wskaźnikowe), które identyfikuje ten obszar. Może być użyte po lewej stronie operatora przypisania (stąd nazwa).

Gdy wprowadzono kwalifikator ```const``` do C, rozróżniono ```lvalue``` od ```modifiable lvalue```.

Termin ```rvalue``` odnosi się do wartości, które można przypisać modyfikowalnym ```lvalue```, ale które same ```lvalue``` nie są. Na przykład w instrukcji:

```c
year = 2024;
```

```year``` jest modyfikowalnym ```lvalue```, a 2024 jest ```rvalue```.



---

## Wskaźniki

Jak pobrać adres zmiennej? operator ```&```:


```c
int n = 4;
double pi = 3.14159;

int *pn = &n;        /* adres zmiennej typu int */
double *ppi = &pi;   /* adres zmiennej typu double */
```
Addres zmiennej typu ```t``` posiada typ ```t*```.

---
## Dereferencja wskaźnika

### Dostęp do zmiennej, do której mamy wskaźnik - operator dereferencji ```*```:

```c
printf("pi = %g\n", *ppi);

*ppi = *ppi + *pn;
``` 

---

## Wskaźniki - rzutowanie


Możemy rzutować wskaźnik danego typu na wskaźnik dowolnego innego typu:

```c
ppi = (double*)pn; // pn jest typu int*
```

Powinniśmy wiedzieć co robimy. Jeśli wskaźnik został rzutowany „na siłę” na inny typ, odczytanie danych może prowadzić np. do niewłaściwej interpretacji bitów.

---

## Wskaźniki - wykorzystanie


- Dzięki wskaźnikom można przekazać do funkcji adres zmiennej.

- Umożliwia to bezpośrednią modyfikację wartości zmiennej w wywołującej funkcji.

- Przykład, kiedy to się przydaje - zamiana wartości:

    ```c
    void swap (int* x, int* y) {
        int temp = *x;
        *x = *y;
        *y = temp;
    }
    ```

---

## Wskaźniki - wykorzystanie

- Identyfikator tablicy w wywołaniu automatycznie przekształca się w wskaźnik do jej pierwszego elementu (*array decays into pointer*).

- W funkcji ```myFunction``` otrzymujemy wskaźnik ```tab``` do pierwszego elementu przekazanej tablicy.

- Aby wiedzieć, ile elementów przetwarzać, musimy przekazać dodatkowy parametr ```size``` – rozmiar tablicy.

    ```c
    void myFunction(int[] tab, int size) {
    //...
    }
    ```

---

## Wskaźnik do wskaźnika ```**```

- wskaźnik to zmienna w pamięci, też znajduje się pod pewnym adresem,
- możemy użyć tego adresu:

    ```c
    int n = 10;
    int* pn = &n; 
    int** ppn = &pn;
    ```
---

## Przykład

```c
void swap_pp(int **a, int **b) {
    int *temp = *a;
    *a = *b;
    *b = temp;
}
```

---
## Tablice wskaźników

```int  *arr[10]``` – tablica wskaźników do ```int```

```char *arr[10]``` – tablica wskaźników do ```char```

---
## Tablice wskaźników - zastosowania

- posiadamy dużą tablicę;
- chcemy mieć ją posortowaną, ale uniknąć wielokrotnego kopiowania danych;

- **tworzymy tablicę wskaźników i ją sortujemy, porównując wartości na które wskazują.** 

---

## Tablice łańcuchów tekstowych

```
char s1[] = "C language"; 
char s2[] = "is";
char s3[] = "awesome"; 

char* strArray[] = {s1, s2, s3};
```

---

## Alokacja pamięci

Każdy program musi przygotować odpowiednią ilość pamięci na dane, których będzie używać. Pamięć na dane zawsze jest alokowana, przy czym: 
- w niektórych przypadkach dzieje się to automatycznie:
    ```c
    char place[] = "Uniwersytet Rzeszowski";
    ```
- w kodzie programu można wprost zadeklarować rozmiar tablicy, np.:
    ```c
    char place[100]; // rozmiar jest znany w momencie kompilacji
    ```
- czasem rozmiar znany jest dopiero w momencie uruchomienia programu - używamy funkcji do **alokacji dynamicznej**.

---

## Dynamiczna alokacja pamięci

### ```malloc()```

- aby użyć ```malloc()``` dołączamy plik nagłówkowy ```<stdlib.h>```;
- jako argument podajemy liczbę bajtów, jaką chcemy zaalokować;
- ```malloc()``` znajduje odpowiednio duży, wolny blok pamięci i zwraca do niego wskaźnik;
- blok pamięci jest anonimowy, odwołujemy się do niego przez wskaźnik.

---

## Dynamiczna alokacja pamięci

```malloc()``` zwraca generyczny wskaźnik typu ```void*```, przed dokonaniem dereferencji operatorem ```*``` należy wskaźnik rzutować na pożądany typ.

```c
double * ptd;  
ptd = (double *) malloc(30 * sizeof(double)); 
```

---

## ```malloc()``` 

- Aby zminimalizować:
  - narzut czasowy związany z alokacją - używamy jej rzadziej, alokujemy od razu większe obszary pamięci,
  - fragmentację pamięci – używamy powtórnie bloków, tam gdzie to możliwe, przydatna funkcja: ```realloc()```.
- Ciągła, niepotrzebna alokacja i zwalnianie pamięci mogą prowadzić do słabej wydajności aplikacji, unikamy takiego kodu 

---

## Konsekwencje

- używamy ```free()``` aby zwolnić pamięć (nie dopuścić do wycieku pamięci).
- nie zwolniona pamięć pozostaje zaalokowana aż do zakończenia działania programu.

---

## ```calloc()```

```c
void* calloc (size_t num, size_t size);
```

Allocate and zero-initialize array.

Allocates a block of memory for an array of num elements, each of them size bytes long, and initializes all its bits to zero.

The effective result is the allocation of a zero-initialized memory block of (num* * *size) bytes.*


---
# Wskaźniki do funkcji

Funkcja:
```c
void ToUpper(char *);   
```
wskaźnik do funkcji:
```c
void (*pf)(char *);  
```
---

## Uwaga na kolejność operatorów!

```c
void (*pf)(char *); // wskaźnik do funkcji

void *pf(char *);   // funkcja zwracająca wskaźnik 
```

---

## Przykłady użycia (poprawnego i nie)

```c
void ToUpper(char *);  
void ToLower(char *);  
int round(double);  
void (*pf)(char *); 
pf = ToUpper;   // prawidłowe
pf = ToLower;   // prawidłowe 
pf = round;     // nieprawidłowe, nie ten typ!
pf = ToLower(); // nieprawidłowe, ToLower() nie jest adresem funkcji!
```

---

## Wskazówka

### *To declare a pointer to a particular type of function, first declare a function of the desired type and then replace the function name with an expression of the form*  ```(*pf)```.

 ### ```pf```  *then becomes a pointer to a function of that type.*

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
