<!--
---
marp: true
footer: "| PwJC | Wykład #3 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/"
_footer: "| e-mail: mkepski@ur.edu.pl |"
paginate: true
_paginate: false
---
-->

# programowanie w języku C
### Wykład #3: Definiowanie funkcji. Wejście/wyjście standardowe.
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | -->

---

<!-- footer: | PwJC | Wykład #3 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/ page_number: true -->

# Funkcje

**Funkcja** - wydzielona część programu, która przetwarza argumenty i może zwracać wartość.

Zwracana wartość może być wykorzystana jako argument w innych funkcjach, stanowić elementy wyrażeń.

---

## Funkcje w programie

```c
#include <stdio.h>
#define SIZE 100

/*...*/

int main(void) {
    float list[SIZE];

    readlist(list, SIZE);
    sort(list, SIZE);
    mean(list, SIZE);
    median(list, SIZE);
    histogram(list, SIZE);

    return 0;
}
```

---

## Definicja funcji - składnia 

```c
typ-zwracany nazwa-funkcji(parametry) {
   ciało-funkcji
}
```

- parametry są opcjonalne, funkcja może ich nie przyjmować,
- jeśli przyjmuje parametry, są postaci: ```typ identyfikator```, np. ```int size```,
- funkcja może nic nie zwracać, wtedy typ zwracany to ```void```.

---

<!--
<style scoped> p {font-size: 10pt} h2 {margin-bottom: 2px} </style>
-->

## Deklarowanie i definiowanie funkcji 

```c
#include <stdio.h>
#define NAME "Uniwersytet Rzeszowski"
#define ADDRESS "al. Tadeusza Rejtana 16c, 35-310 Rzeszów"
#define WIDTH 40

void line_separator(void); /* prototyp funkcji */

int main(void) {
    line_separator();
    printf("%s\n", NAME);
    printf("%s\n", ADDRESS);
    line_separator(); /* wywołanie funkcji */
    return 0;
}
void line_separator(void) { /* definicja funkcji */
    for (int i = 0; i <= WIDTH; i++) {
        printf("*");
    }
    printf("\n");
}
```
&nbsp;

---

## Deklaracja funkcji - składnia

```
typ-zwracany nazwa-funkcji(parametry);
```

- deklarację nazywamy inaczej prototypem funkcji,

- na liście parametrów muszą występować typy, identyfikatory nie są obowiązkowe,

- deklarację umieszczamy powyżej pierwszego wywołania funkcji,

- można też umieścić ją w pliku nagłówkowym, a następnie użyć dyrektywy ```#include```.

---

## Funkcje (z parametrami)

<!--
<style scoped> p {font-size: 10pt} h2 {margin-bottom: 2px} </style>
-->

```c
#include <stdio.h>
#define NAME "Uniwersytet Rzeszowski"
#define ADDRESS "al. Tadeusza Rejtana 16C, 35-310 Rzeszów"
#define WIDTH 40
void line_separator(int width); /* prototyp funkcji */

int main(void) {
    line_separator(WIDTH);
    printf("%s\n", NAME);
    printf("%s\n", ADDRESS);
    line_separator(WIDTH); /* wywołanie funkcji, 40 jest argumentem */
    return 0;
}
void line_separator(int width) { /* definicja funkcji, width to parametr */
    for (int i = 0; i <= width; i++){
        printf("*");
    }
    printf("\n");
}
```
&nbsp;

---

## Parametry

Argument (czasem też zwany argumentem aktualnym) to wyrażenie, które pojawia się w nawiasach podczas wywołania funkcji.

Parametr (czasem też zwany parametrem formalnym) to zmienna zadeklarowana w nagłówku definicji funkcji.

Gdy funkcja zostaje wywołana, zmienne zadeklarowane jako parametry formalne są tworzone i inicjalizowane wartościami uzyskanymi w wyniku obliczenia argumentów aktualnych.

___

## Zwracanie wartości z funkcji

### Słowo kluczowe ```return```:

```
return wyrażenie;
```
przykład:

```c
int imin(int a, int b) {
    return (a < b) ? a : b;
}
```

---

## Zakres zmiennych

Zmienne zadeklarowane na poziomie pliku mają zakres **globalny**. 

Zmienne zadeklarowane w bloku kodu ```{}``` mają zakres **lokalny** &ndash; istnieją tylko dla tego bloku (od miejsca deklaracji do końca bloku).

---

## Zakres zmiennych

```c
#include <stdio.h>

int count = 0; // zmienna globalna

int main(void) {
    printf("%d ", count);
    count = 1; // zmienna lokalna, przysłania globalną 'count'
    printf("%d ", count);
    return 0;
}
```

```
0 1
```

---

## Zakres zmiennych

```c
#include <stdio.h>

int count = 0; // zmienna globalna

int print_count(){ printf("%d ", count); }

int main(void) {
    print_count();
    count = 1; // modyfikujemy zmienną globalną
    print_count();
    return 0;
}
```

```
0 1
```

---

## Zakres zmiennych

```c
#include <stdio.h>

int count = 0; // zmienna globalna

int print_count(){ printf("%d ", count); }

int main(void) {
    int count = 10; // zmienna lokalna 
    printf("%d ", count); 
    print_count();
    count = 1; 
    printf("%d ", count);
    print_count();
    return 0;
}
```
```
10 0 1 0
```

---

## Zakres zmiennych

```c
#include <stdio.h>

int print_count(){ printf("%d ", count); }

int main(void) {
    int count = 10;
    printf("%d ", count);
    print_count();
    count = 1;
    printf("%d ", count);
    print_count();
    return 0;
}
```
```text 
error: ‘count’ undeclared (first use in this function)
```

---

## Zakres zmiennych

Powinniśmy wiedzieć:
- **jak rozróżniać zmienne globalne od lokalnych,**
- **jak lokalne zmienne przesłaniają globalne,**
- jak działa inicjalizacja zmiennych i wartości początkowe,
- czym są tzw. *Storage Class Specifiers* - klasy pamięci zmiennych.

---

## *Storage class specifiers*: ```auto```

Specyfikator określa, że zmienna ma automatyczny czas życia (*automatic storage duration*) – istnieje tylko w obrębie bloku (najczęściej funkcji), w którym została zadeklarowana.

Jest to domyślny specyfikator dla zmiennych lokalnych (wewnątrz funkcji, bez ```static``` czy innych słów kluczowych).

Istnieje słowo kluczowe ```auto``` ale rzadko się go używa, bo taki czas życia dla zmiennych lokalnych jest domyślny, więc ```auto``` jest zbędne.

---

## *Storage class specifiers*: ```register```

Słowo kluczowe ```register``` to wskazówka dla kompilatora, że zmienna będzie często używana, być może opłacalne jest  zarezerwowanie dla niej rejestru procesora.

Przykładowa deklaracja zmiennej:

```c
register double d;
```

*Uwaga:* w praktyce kompilatory mają zaawansowane mechanizmy opytmalizacji kodu, współcześnie nie jest zalecane używanie ```register```.

Zmienne z deklaratorem ```register``` mają automatyczny czas życia (*automatic storage duration*).

---

## *Storage class specifiers*: ```static```

Słowo kluczowe ```static``` użyte przy deklaracji zmiennej lokalnej sprawia, że zmienna zachowuje swoją wartość po wyjściu z bloku/funkcji. Zmienne takie mają statyczny czas życia.

Zmienne globalne (zadeklarowane poza wszystkimi funkcjami, bez słowa kluczowego ```static```) również mają statyczny czas życia (*static storage duration*).

---

## Wartości początkowe i inicjalizacja

*If  an  object  that  has  automatic  storage  duration  is  not  initialized  explicitly, its value is indeterminate.*

*If an  object  that  has  static  storage  duration  is  not  initialized  explicitly, then:*
- *if it has pointer type, it is initialized to a null pointer;*
- *if it has arithmetic type, it is initialized to (positive orunsigned) zero;*


ISO/IEC 9899:TC3 WG14/N1256 

---

## Wartości początkowe i inicjalizacja

- Zmienne globalne i statyczne po deklaracji są domyślnie ustawiane na 0 (typ arytmetyczny) lub na *null pointer* (typ wskaźnikowy).

- Zmienne lokalne nie mają gwarancji inicjalizacji na konkretną wartość, sami musimy ją zainicjalizować.

---

##  zmienna lokalna z ```static``` - przykład

```c
#include <stdio.h>

void function_with_static_variable() { 
    static int var;
    printf("%d ", var); 
    var++;
}

int main() {
    function_with_static_variable();
    function_with_static_variable();
    function_with_static_variable();
    return 0;
}
```

```
0 1 2
```

---

## Rekurencja (rekursja)

C pozwala funkcji wywoływać samą siebie. Takie wywołanie nazywamy **rekurencyjnym**.

Przy używaniu rekurencji trzeba zadbać, aby ciąg wywołań skończył się. 

Zwykle przed wywołaniem samej siebie funkcja sprawdza jakiś warunek i jeśli nie jest on spełniony, nie dokonuje już samo-wywołania. 

Rekurencję można często stosować tam, gdzie można też użyć pętli. Czasem rozwiązanie z pętlą jest bardziej oczywiste; czasem bardziej oczywiste jest rozwiązanie rekurencyjne.

---

## Rekurencja - przykład

Obliczanie silni:

```c
unsigned long silnia(unsigned long n) {
    if(n > 1)
        return n * silnia(n - 1);
    else
        return 1;
}
```

---

## Rekurencja - przykłady

---

# Wejście/wyjście standardowe

- Funkcje ```printf()``` i ```scanf()``` pozwalają na komunikację użytkownika z programem.
- Składnia wykorzystuje tzw. *format specifiers* do kontroli formatu danych.

---

## ```printf```

```c
int printf(const char * format, ...);
```
```format```:
- zawiera tekst, który ma zostać wypisany na ekran,
- może zawierać *format specifiers*, które opisują, jak wypisać wartości dodatkowych argumentów, znajdujących się po format (opcjonalne).

---

## ```printf```

<!--
<style scoped> pre { background-color: transparent; border-color: transparent;} </style>
-->


```c

                              control-string         zmienne, tyle ile format specifiers
                                     |                   |
                                     |                   |
                                     |                   |
                                     |                   |
                 ----------------------------------      V
         printf("Rozpoczynamy losowanie %d liczb \n" , count);       
                                         ^
                                         |
                                         |
                                         |
                                         |
                               format specifier w łańcuchu tekstowym


```

---

<!--
<style scoped>
td { background-color: #ffffffff;}
th { border-top-color: #ffffffff; border-left-color: #ffffffff; border-right-color: #ffffffff;}
</style>
-->

## *format specifiers*
|  |  |
|----|:------------------------|
| ```%c``` | pojedynczy znak (```char```) |
| ```%d``` | liczba całkowita ze znakiem w formacie dziesiętnym |
| ```%e``` | liczba zmiennoprzecinkowa w zapisie naukowym (1.2345e+2) |
| ```%E``` | liczba zmiennoprzecinkowa w zapisie naukowym (1.2345E+2) |
| ```%f``` | liczba zmiennoprzecinkowa w formacie dziesiętnym |

---

<!--
<style scoped>
td { background-color: #ffffffff;}
th { border-top-color: #ffffffff; border-left-color: #ffffffff; border-right-color: #ffffffff;}
</style>
-->

## *format specifiers*
|  |  |
|----|------------------------|
| ```%i``` | liczba całkowita ze znakiem w formacie dziesiętnym |
| ```%o``` | liczba całkowita bez znaku w formacie ósemkowym |
| ```%p``` | wskaźnik |
| ```%s``` | łańcuch tekstowy |
| ```%u``` | liczba całkowita bez znaku w formacie dziesiętnym |
| ```%x``` | liczba całkowita bez znaku w formacie szesnastkowym, małe litery (```0f```) |
| ```%X``` | liczba całkowita bez znaku w formacie szesnastkowym, wielkie litery (```0F```) |
| ```%%``` | wypisuje znak procent (```%```) |

---

<!--
<style scoped>pre {margin-top: 30px; margin-bottom: 30px}</style>
-->

## Dokładna kontrola formatowania 

&nbsp;
Prototyp *format specifier* wygląda następująco:

```
%[flags][width][.precision][length]specifier
```

Dodatkowe, opcjonalne komponenty pozwalają sterować formatowaniem, np. ustawiać szerokość, liczbę cyfr po znaku dziesiętnym, uzupełniać wiodącymi zerami itd.

---

## Przykład

``` c
#include <stdio.h>

int main() {
    int a;

    printf("Pojedyncze znaki: %c %c \n", 'a', 65);
    printf("Liczby w systemie dziesietnym : %d %i\n", 1984, 1984);
    printf("Poprzedzone spacjami: %10d \n", 2020);
    printf("Poprzedzone zerami  : %010d \n", 2020);
    printf("Zmiennoprzecinkowe: %4.2f %+.0e %E \n", 3.141, 3.141, 3.141);
    printf("%s \n", "Tekst");

    scanf("%d", &a);
    return 0;
}
```

---

## Przykład

```c
// ...
    printf("Pojedyncze znaki: %c %c \n", 'a', 65);
    printf("Liczby w systemie dziesietnym : %d %i\n", 1984, 1984);
    printf("Poprzedzone spacjami: %10d \n", 2020);
    printf("Poprzedzone zerami  : %010d \n", 2020);
    printf("Zmiennoprzecinkowe: %4.2f %+.0e %E \n", 3.141, 3.141, 3.141);
    printf("%s \n", "Tekst");
//...
```

```text
Pojedyncze znaki: a A 
Liczby w systemie dziesietnym: 1984 1984
Poprzedzone spacjami:       2020 
Poprzedzone zerami  : 0000002020 
Zmiennoprzecinkowe: 3.14 +3e+00 3.141000E+00 
Tekst
```

---

## ```printf``` - uwagi

- Zwraca wartość - liczbę wypisanych znaków, chyba że wystąpił błąd - wtedy zwraca liczbę ujemną.

- Dodatkowych argumentów powinno być przynajmniej tyle, ile *format specifiers* w łańcuchu tekstowym.

- Nadmiarowe argumenty są ignorowane.

- Stała szerokość wypisywanych liczba (poprzedzanie ich spacjami) jest przydatne przy wypisywaniu kolumn danych.

---

## ```scanf```

- służy do wczytywania,
- oczekiwanym parametrem są **wskaźniki** do zmiennych, do których będziemy wczytywać,
- składnia podobna do ```printf```.

---

## ```scanf```

```c
int scanf(const char * format, ...);
```

---

## ```scanf``` - przykład

```c
#include <stdio.h>

int main() {
  char str[80];
  int i;

  printf("Podaj swoje imie: ");
  scanf("%s", str); // w przypadku tablic podajemy ich identyfikator
  printf("Podaj swoj wiek: ");
  scanf("%d", &i);  // dla innych typów musimy użyć & aby przekazać wskaźnik
  printf(" %s,  masz %d lat.\n", str, i);

  return 0;
}
```

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
