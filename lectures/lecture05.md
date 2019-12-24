
# Podstawy programowania w języku C
### Wykład #5: Struktury, unie. Zarządzanie pamięcią
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | 
-->

---
<!-- footer: | PPwJC | Wykład #5 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/
     page_number: true -->

# Powtórka

- Wskaźnik - przechowuje adres w pamięci
- operator adresowania ```&```
- ```int* px = &x```
- arytmetyka wskaźników 

---

# Wskaźnik do wskaźnika ```**```

- wskaźnik to zmienna w pamięci, też znajduje się pod pewnym adresem
- możemy użyć tego adresu:
```
int n = 10;
int* pn = &n; 
int** ppn = &pn;
```
---

# Przykład

```
void swap_pp(int **a, int **b) {
	int *temp = *a;
	*a = *b;
	*b = temp;
}

```

---
# Tablice wskaźników

```int*  arr[10]``` – tablica wskaźników do int
```char* arr[10]``` – tablica wskaźników do char

---
# Tablice wskaźników - zastosowania

- posiadamy dużą tablicę;
- chcemy mieć ją posortowaną, ale uniknąć wielokrotnego kopiowania danych;

- **tworzymy tablicę wskaźników i ją sortujemy, porównując wartości na które wskazują.** 

---
# Tablice łańcuchów tekstowych

```
char s1[] = "C language"; 
char s2[] = "is";
char s3[] = "awesome"; 

char* strArray[] = {s1, s2, s3};
```

---
# Alokacja pamięci

- każdy program musi przygotować odpowiednią ilość pamięci na dane, których będzie używać;
- w niektórych przypadkach dzieje się to automatycznie:
 ```
 float x; 
 char place[] = "Uniwersytet Rzeszowski";
```
- w kodzie programu można też zadeklarować rozmiar tablicy, np.:
```
char place[100];
```
---
# Dynamiczna alokacja pamięci

## ```malloc()```

- jako argument podajemy liczbę bajtów, jaką chcemy zaalokować;
- ```malloc()``` znajduje odpowiednio duży, wolny blok pamięci i zwraca do niego wskaźnik;
- blok pamięci jest anonimowy, odwołujemy się do niego przez wskaźnik.


---
# Dynamiczna alokacja pamięci

```malloc()``` zwraca generyczny wskaźnik typu ```void*```, przed dokonaniem dereferencji operatorem ```*``` należy wskaźnik rzutować na pożądany typ.

```
double * ptd;  
ptd = (double *) malloc(30 * sizeof(double)); 
```

---
# malloc() 

- aby zminimalizować:
  - narzut czasowy związany z alokacją, używamy jej rzadziej, alokujemy od razu większe obszary pamięci
  - fragmentację pamięci – używamy powtórnie bloków, tam gdzie to możliwe, przydatna funkcja: ```realloc()```
- ciągła, niepotrzebna alokacja i zwalnianie pamięci mogą prowadzić do słabej wydajności aplikacji, unikamy takiego kodu 

---
# Konsekwencje

- używamy ```free()``` aby zwolnić pamięć (nie dopuścić do wycieku pamięci).
- nie zwolniona pamięć pozostaje zaalokowana aż do zakończenia działania programu.
---
# ```calloc()```
```c
void* calloc (size_t num, size_t size);
```
*Allocate and zero-initialize array.
Allocates a block of memory for an array of num elements, each of them size bytes long, and initializes all its bits to zero.
The effective result is the allocation of a zero-initialized memory block of (num* * *size) bytes.*


---
# Wskaźniki do funkcji

funkcja:
```
void ToUpper(char *);   
```
wskaźnik do funkcji:
```
void (*pf)(char *);  
```
---
# Uwaga na kolejność operatorów!

```
void (*pf)(char *); // wskaźnik do funkcji

void *pf(char *);   // funkcja zwracająca wskaźnik 

```
---
# Przykłady użycia (poprawnego i nie)

```
void ToUpper(char *);  
void ToLower(char *);  
int round(double);  
void (*pf)(char *); 
pf = ToUpper;   // prawidłowe
pf = ToLower;   // prawidłowe 
pf = round;     // nieprawidłowe, nie ten typ!
pf = ToLower(); // nieprawidłowe, ToLower()
                // nie jest adresem funkcji
```
---
# Wskazówka

### *To declare a pointer to a particular type of function, first declare a function of the desired type and then replace the function name with an expression of the form  ```(*pf)```  pf  then becomes a pointer to a function of that type.*

---
# Struktury

- grupują dane
- musimy zdefiniować jak struktura będzie zbudowana, np:

```
struct point2i {
   int x;
   int y;
};
```
---
# Struktury

- ```struct``` definiuje nowy typ danych
- nazwa struktury jest opcjonalna: ```struct {...} x,y,z;```
- zmienne mające typ danych naszej struktury mogą być deklarowane jak każde inne:

```
struct point2i ptA; 
```

---
# Struktury

- operator przypisania kopiuje każdy element struktury - uwaga na wskaźniki!
- elementy mogą być strukturami

```
struct triangle {
   struct point2i ptA;
   struct point2i ptB;
   struct point2i ptC;
};
```

---

# Struktury

- do elementów struktury dostajemy się operatorem ```.```.
- jeśli struktura jest zagnieżdżona, wiele ```.``` będzie konieczne.


--- 

# Struktury

- Dla dużych struktur warto rozważyć użycie wskaźnika, przy przekazywaniu ich do funkcji:

```
void foo(struct poin2i* pp);
```
- na wskaźnikach operator ```->``` pozwala na uzyskanie dostępu do elementów struktury;

---

# Struktury

*The size of a structure is greater than or equal to the sum
of the sizes of its members.*

---

# Unie

Unia jest typem złożonym, który może przechowywać wiele typów danych w jednej lokalizacji pamięci. 

---
# Unie

Jednak pola unii nakładają się na siebie, w danej chwili można w niej przechowywać wartość tylko jednego typu. Przykład: 

```
union data {
   int i data ;
   float fdata ;
   char* sdata ;
} d1, d2, d3; 

d1.idata =10;
d1.fdata =3.14F;
d1.sdata="hello world"; 
```

---
# Unie

Miejsce na unię jest tak zwymiarowane przez kompilator, aby mogło pomieścić największą składową. 

Umożliwia to oszczędne gospodarowanie pamięcią i jednocześnie manipulowanie danymi różnego rodzaju w tym samym miejscu pamięci.



