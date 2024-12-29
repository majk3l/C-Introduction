<!--
---
marp: true
footer: "| PwJC | Wykład #6 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/"
_footer: "| e-mail: mkepski@ur.edu.pl |"
paginate: true
_paginate: false
---
-->

# programowanie w języku C
### Wykład #6: Biblioteka standardowa. Operacje na plikach.
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | -->

<!-- footer: | PwJC | Wykład #6 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/ page_number: true -->

---

# Biblioteka standardowa

Każda funkcja biblioteczna jest deklarowana w nagłówku, którego zawartość dołącza się za pomocą ```#include```. Nagłówek zawiera zestaw powiązanych funkcji, niezbędne typy oraz makra ułatwiające ich użycie.

Nagłówki:

```
<assert.h>    <complex.h>     <ctype.h>     <errno.h>     <fenv.h>
<float.h>     <inttypes.h>    <iso646.h>    <limits.h>    <locale.h>
<math.h>      <setjmp.h>      <signal.h>    <stdarg.h>    <stdbool.h>
<stddef.h>    <stdint.h>      <stdio.h>     <stdlib.h>    <string.h>
<tgmath.h>    <time.h>        <wchar.h>     <wctype.h>
```

---

## Biblioteka standardowa

### Dokonamy przeglądu kilku zagadnień/zadań/problemów pojawiających się podczas pisania programów (nie tylko w języku C).

### Zaprezentowane zostaną możliwości biblioteki standardowej, która dostarcza funkcjonalności, pomocnych w rozwiązywaniu powyższych.

---

# Przenośność kodu

- Rozmiar typów jest zależny od maszyny, na której kompilujemy kod.

- Chcemy pisać kod uniwersalny (być może programujemy jakieś urządzenia wbudowane, albo fragmenty kodu działają na różnych platformach).

---

##  ```stdint.h``` oraz ```inttypes.h```

- tworzą dodatkowe aliasy dla typów,
- zapewniają stałą długość typu w bitach na róznych systemach.

### implementacja a interfejs:

- interfejs: otrzymujemy typy o zagwarantowanym rozmiarze;
- implementacja: rózna na róznych platformach.

---

##  ```stdint.h```

```c
int8_t        // typ całkowity ze znakiem o szerokości 
int16_t       // dokładnie 8, 16, 32 lub 64 bity,
int32_t       // bez bitów wypełnienia, z wykorzystaniem kodu
int64_t       // uzupełnień do dwóch dla liczb ujemnych
              
int_fast8_t   // najszybszy typ całkowity ze znakiem
int_fast16_t  // o szerokości co najmniej 
int_fast32_t  // 8, 16, 32 lub 64 bity
int_fast64_t  

int_least8_t  // najmniejszy typ całkowity ze znakiem
int_least16_t // o szerokości co najmniej 
int_least32_t // 8, 16, 32 lub 64 bity
int_least64_t 

// podobnie dla typów bez znaku: uint...
```

---

##  ```stdint.h```

#### Kiedy używać?:

- piszemy kod na wiele platform,
- robimy operacje na poszczególnych bitach, np. zapisujemy 4x 8 bitów w jednej zmiennej.

---

##  ```stdint.h```

### Kiedy używać?

```c
// chcemy zapisać kolor (A,R,G,B) (255, 10, 255, 255)
// na zmiennej argb

int argb = ((255 << 24) | (10 << 16) | (255 << 8) | 255);

```
na architekturze x86 zadziała, rozmiar int powinnen wynosić 32 bit.

---

##  ```stdint.h```

### Kiedy używać?

```c
// chcemy zapisać kolor (A,R,G,B) (255, 10, 255, 255)
// na zmiennej argb

int argb = ((255 << 24) | (10 << 16) | (255 << 8) | 255);

```
Microchip XC16: rozmiar ```int``` 16 bit, tu pojawia się problem.

---

##  ```stdint.h```

### Bezpieczniejszy kod

```c
#include <stdint.h>

// chcemy zapisać kolor (A,R,G,B) (255, 10, 255, 255)
// na zmiennej argb

uint32_t argb = ((255 << 24) | (10 << 16) | (255 << 8) | 255);
```

---

## Dygresja

### zadanie dla dociekliwych:

jak odczytać poszczególne kanały (A,R,G,B) ze zmiennej ```uint32_t argb```, która przechowuje wartości kolorów w ten sposób?

```
 31                                   0
  |AAAAAAAA|RRRRRRRR|GGGGGGGG|BBBBBBBB|

```
Używamy operacji bitowych, podobnie jak przy zapisie.

---

## ```limits.h```

- Zawiera stałe określające zakresy podstawowych typów całkowitych i limitów środowiska kompilatora/maszyny.

- Pozwala przenośnie (w sposób niezależny od platformy) sprawdzić maksymalne i minimalne wartości, jakie mogą przyjmować różne typy w danym środowisku.

### Przykładowe makra

```INT_MIN``` i ```INT_MAX``` — minimalna i maksymalna wartość typu ```int```.

```ULONG_MAX``` — maksymalna wartość typu ```unsigned long```.

```CHAR_BIT``` — liczba bitów w typie ```char```.

---

## ```cfloat.h```

### Charakterystyka typów zmiennoprzecinkowych

*Ten plik nagłówkowy opisuje charakterystykę typów zmiennoprzecinkowych na określonym systemie i dla konkretnego kompilatora.*

---

## ```cfloat.h``` - rzeczy warte uwagi

### Precyzja obliczeń zmiennoprzecinkowych!

Upakowanie (ściśnięcie) nieskończonej liczby liczb rzeczywistych w skończoną liczbę bitów wymaga reprezentacji przybliżonej.


```FLT_EPSILON``` - Różnica między 1 a najmniejszą liczbą większą od 1, którą można przedstawić.

---

## ```FLT_EPSILON``` 

```c
float a, b;
//...
if(a == b) {   /// <- tak nigdy nie robimy!
//...
}

```

---

## ```FLT_EPSILON```  

```c
float a, b;
//...
if(fabs(a - b) < EPSILON) {   /// <- tak jest lepiej
//...
}

```

czy ```EPSILON``` powinien być równy ```FLT_EPSILON``` ?

Przy wartościach w przedziale [-2 2] tak, dla liczb o większej wartości bezwzględnej niż 2 trzeba go przeskalować.

---

## Epsilon maszynowy...

###  ... dla  dociekliwych:

>David Goldberg. 1991. What every computer scientist should know about floating-point arithmetic. ACM Comput. Surv. 23,5-48. DOI: https://doi.org/10.1145/103162.103163 

---

## ```cfloat.h```

Zawiera stałe definiujące limity maszynowo-kompilacyjne dla liczb zmiennoprzecinkowych w danym środowisku. Przykładowe definicje:

```FLT_MAX```, ```DBL_MAX```, ```LDBL_MAX``` — wartości maksymalne typów ```float```, ```double```, ```long double```.

```FLT_DIG```, ```DBL_DIG```, ```LDBL_DIG``` — liczba dziesiętnych cyfr precyzji (maks. pewna liczba cyfr, które można zapisać bez utraty).

Dzięki temu możemy pisać kod bardziej odporny na błędy związane z przekraczaniem zakresu.

---

# Operacje na znakach

Operujemy na znakach i chcemy wygodnie wykonywać na nich podstawowe operacje, np.:

- sprawdzić czy są literami, cyframi, wielkimi literami, itd.

- modyfikować je (np. konwertować na małe znaki).

---

## ```ctype.h```

Ten plik nagłówkowy deklaruje kilka funkcji przydatnych do testowania i mapowania znaków. 


```
isalnum isalpha isblank iscntrl isdigit isgraph islower isprint

ispunct isspace isupper isxdigit tolower toupper
```

--- 

## ```ctype.h``` - przykład

```c
int isalpha ( int c );
```

*Checks whether c is an alphabetic letter.*

**Param:**
&nbsp;&nbsp; *Character to be checked, casted to an int, or EOF.*

**Return value:**

&nbsp;&nbsp; *A value different from zero (i.e., true) if indeed c is an alphabetic letter. Zero (i.e., false) otherwise.*

---

# Konwersje do formatów numerycznych

- Wczytaliśmy dane do łańcucha tekstowego, potrzebujemy zamienić je na liczby.

- Łańcuch tekstowy może zawierać jednocześnie tekst i liczby, naprzemiennie.

- Co więcej, liczby mogą być różnego typu i zapisane w różnym formacie.

---

## ```<stdlib.h>```

Dwie podstawowe funkcje, które warto znać:

- ```atoi()``` - do wczytywania liczb całkowitych,

- ```atof()``` - do wczytywania liczb zmiennoprzecinkowych.

---

## ```atoi()```

Konwertuje wartość zapisaną w łańcuchu znaków do postaci liczby typu całkowitego (```int```, ```long``` lub ```long long``` w zależności od wariantu).

Warianty funkcji:

```c
int atoi(const char *str);
		
long atol(const char *str);
		
long long atoll(const char *str);
```

---

## ```atoi()```

```c
int atoi(const char *str);
```

*Discards any whitespace characters until the first non-whitespace character is found.*  

*Takes as many characters as possible to form a valid integer number representation and converts them to an integer value.*

*The valid  value consists of the following parts:*
  - *plus or minus sign (optional),*
  - *numeric digits.*

---

## ```atoi()```

```c
int atoi(const char *str);
```
**Parameters:**

```str``` - *pointer to the null-terminated byte string to be interpreted*

**Return value:**

*Integer value corresponding to the contents of ```str``` on success. If the converted value falls out of range of corresponding return type, the return value is undefined. If no conversion can be performed, ​```0```​ is returned.*

---

## ```atof()```

Działa na podobnej zasadzie, rozpatruje kilka notacji liczb zmiennoprzecinkowych, a także ```nan``` oraz ```inf```.

### ```inf``` i ```nan```

```nan``` - reprezentuje wynik nie mający interpretacji liczbowej.
```inf``` - reprezentuje dodatnią nieskończoność, jest równe wartości, które są rezultatem operacji matematycznych takich jak ```1.0 / 0.0```.

---

# Operacje na łańcuchach tekstowych

- Potrzebujemy metod do manipulowania tekstem.  

- Typowe operacje będą sprowadzać (ale nie ograniczać się do): kopiowania, porównywania, wyszukiwania.

---

## ```string.h```

- ```strcpy ``` - kopiuje łańcuch znaków do tablicy znaków

- ```strncpy``` - kopiuje określoną liczbę znaków łańcucha do tablicy znaków

- ```strlen ``` - oblicza długość łańcucha znaków

- ```strcmp ``` - porównuje dwa łańcuchy znaków 

- ```strncmp``` - porównuje określoną liczbę znaków dwóch łańcuchów

- ```strchr ``` - szuka pierwszego wystąpienia znaku w łańcuchu

- ```strrchr``` - szuka ostatniego wystąpienia znaku w łańcuchu

- ```strstr ```  - szuka pierwszego wystąpienia łańcucha znaków w innym łańcuchu 

---

## ```strcpy()```

```c
char *strcpy(char *dest, const char *src);
```

*Copies the null-terminated byte string pointed to by src, including the null terminator, to the character array whose first element is pointed to by dest.*

*The behavior is undefined if the dest array is not large enough. The behavior is undefined if the strings overlap. The behavior is undefined if either dest is not a pointer to a character array or src is not a pointer to a null-terminated byte string.*

---

## ```strcpy()```

```c
char *strcpy(char *dest, const char *src);
```

### Parameters

```dest``` 	- 	*pointer to the character array to write to*,
```src ``` 	- 	*pointer to the null-terminated byte string to copy from*.

### Return value

*Returns a copy of* ```dest```.


---

## Inne pliki nagłówkowe

### ```<math.h>``` - wybrane funkcje:

- ```sin(x)```, ```cos(x)```, ```tan(x)``` – sinus, cosinus, tangens (argument w radianach).
- ```exp(x)``` – funkcja wykładnicza.
- ```log(x)```, ```log10(x)``` – logarytm naturalny, logarytm dziesiętny.
- ```sqrt(x)``` – pierwiastek kwadratowy.
- ```pow(x, y)``` – oblicza x do potęgi y.
- ```fabs(x)``` – wartość bezwzględna.
- ```ceil(x)```, ```floor(x)``` – zaokrąglenie w górę i zaokrąglenie w dół

---

## Inne pliki nagłówkowe

### ```<time.h>``` - wybrane funkcje:

- ```time_t time(time_t *t)``` - zwraca liczbę sekund od 1 stycznia 1970 (Unix Epoch).
        
- ```struct tm *localtime(const time_t *t)``` - czas w sekundach -> czas  lokalny.

- ```struct tm *gmtime(const time_t *t)   ``` -  czas w sekundach -> czas UTC.

- ```char *asctime(const struct tm *tm)   ``` - struktura ```tm``` -> czytelny format tekstowy, przykładowo: ```"Mon Jan 1 11:00:00 2024"```.

- ```double difftime(time_t end, time_t start)``` - różnica czasu między ```end```a ```start```.

---

# Operacje na plikach

---

## Czym jest plik?

- Plik jest nazwaną sekcją pamięci trwałej, służy do przechowywania danych.
- Z poziomu języka C jest on ciągłą sekwencją bajtów, z której każdy może zostać osobno odczytany.

---

## Pliki binarne a pliki tekstowe

- Cała zawartość pliku jest w formie binarnej. 

- Jeśli plik używa kodów binarnych dla znaków (ASCII lub Unicode) do reprezentacji tekstu, to jest to plik o zawartości tekstowej.

- Jeśli wartości binarne reprezentują kod języka maszynowego, dane numeryczne czy kodowanie obrazu lub muzyki, to mówimy, że zawartość pliku jest binarna.

---

## Pliki binarne a pliki tekstowe

- Różne systemy używają różnych kodów do oznaczenia końca linii - Linux i MacOS ```\n``` a MS Dos czy Windows ```\r\n```.

- Jeśli plik tekstowy zostaje otwarty w trybie tekstowym to biblioteka C zapewnia konwersję tych symboli do ```\n```.

- Jeśli plik tekstowy otworzymy w trybie binarnym, dostaniemy takie symbole jakie zostały zapisane (nie ma żadnej konwersji).

---

## Buforowanie

- Zawartość pliku nie jest wczytywana bajt po bajcie, lecz kopiowana do tymczasowego miejsca w pamięci, zwanego buforem.
- Pozwala to znacznie przyśpieszyć operacje I/O.
- Poszczególne elementy bufora mogą być pojedynczo analizowane.

---

## Pliki – podstawowe informacje

- C postrzega każdy plik jako sekwencyjny strumień bajtów. 
- Każdy plik kończy się albo znacznikiem końca pliku, albo określonym numerem bajtu zapisanym w systemowej administracyjnej strukturze danych. 
- Gdy plik jest otwierany, kojarzony jest z nim strumień. 
- 3 pliki i związane z nimi strumienie są automatycznie otwierane podczas uruchomienia programu - standardowe wejście (```stdin```), standardowe wyjście (```stdout```) i standardowy błąd (```stderr```).

---

## ```<stdio.h>```

Oprócz poznanych ```printf()``` i ```scanf()``` zawiera szereg funkcji:

```
fopen()    getc()     putc()     exit()    fclose()  fprintf()
fscanf()   fgets()    fputs()    rewind()  fseek()   ftell()
fflush()   fgetpos()  fsetpos()  feof()    ferror()  ungetc()
setvbuf()  fread()    fwrite()
```

---

## ```fopen()```

- Funkcja zadeklarowana w ```stdio.h```.

- Pierwszy argument to nazwa pliku do otwarcia, drugi argument to łańcuch określający tryb, w jakim plik ma być otwarty. 

- Po pomyślnym otwarciu pliku ```fopen()``` zwraca wskaźnik na plik, w przeciwnym razie zwraca pusty wskaźnik.

- Funkcje I/O mogą następnie użyć wskaźnika do operacji na pliku, wskaźnik jest typu ```FILE*```, a ```FILE``` jest typem pochodnym zdefiniowanym w ```stdio.h```.

- Wskaźnik nie wskazuje na rzeczywisty plik, lecz na obiekt danych zawierający informacje o pliku oraz informacje o buforze.

--- 

## Wczytywanie po znaku: ```getc()```

```c
int getc(FILE* stream);
```

Wczytuje kolejny znak ze wskazanego strumienia.

### Parametry 
```stream``` - określa strumień na którym ma zostać wykonana operacja.

### Wartość zwracana

Zwraca kod odczytanego znaku (konwertowany do ```int```) w przypadku sukcesu. Funkcja zwraca wartość ```EOF``` w przypadku napotkania błędu lub osiągnięcia końca strumienia. W celu ustalenia rodzaju błędu jaki wystąpił należy użyć funkcji ```ferror()``` lub ```feof()```.

---

## End-of-file

- Program odczytujący dane musi się zatrzymać po osiągnięciu końca pliku. 
- Funkcja ```getc()``` zwraca specjalną wartość ```EOF```, jeśli próbuje odczytać jakiś znak i odkrywa, że dotarła do końca pliku.

- Aby uniknąć próby odczytania z pliku po osiągnięciu jego końca, można stosować przykładowo następujący warunek pętli:

```c
while (( ch = getc(fp)) != EOF)
```

---

## ```fclose()```

- Zamyka plik określony przez parametr (wskaźnik do pliku).
- W razie potrzeby opróżnia wcześniej bufor (operacja *flush*). 
- Zwraca wartość ```0``` jeśli zamknięcie pliku się udało, w przeciwnym razie zwraca ```EOF```.

---

##  Prosty przykład - zapisywanie

```c
#include <stdio.h>

int main() {
   FILE *pF = fopen("example00.txt", "w");
   fprintf(pF, "Test text");
   fclose(pF);

   return 0;
}
```

---

## ```fprintf()```

```c
int fprintf(FILE* stream, const char *format, ...);
```

Funkcja zapisuje tekst sformatowany do wskazanego strumienia ```stream```.

### Parametry
```stream``` -  strumień na którym ma zostać wykonana operacja
```format``` -  tekst sformatowany, działa jak w funkcji ```printf()```
```...``` 	 - 	opcjonalne argumenty

### Wartość zwracana

Zwraca liczbę bajtów zapisanych do strumienia lub wartość ujemną w przypadku błędu.

---

##  Mode strings

<!--
<style scoped>
table { height: 55%; width: 100%; font-size: 15pt; color: black; }
th { color: blue; }
</style>
-->

| Kod | Znaczenie | Co robi z plikiem | jeśli plik istnieje | jeśli plik nie istnieje |
|:----:|:------:|:-----------------------:|:-------------------:|:-----------:|
| "r"  | odczyt | otwiera do odczytu |  czyta od początku  |  błąd       |
| "w"  | zapis  | tworzy do zapisu   |  usuwa zawartość    | tworzy nowy |
| "a"  | dopisywanie  | dopisuje do pliku |  dopisuje do końca  | tworzy nowy |
| "r+" | odczyt + zapis | otwiera do odczytu/zapisu | czyta od początku |  błąd  |
| "w+" | zapis + odczyt  |  tworzy plik do zapisu + możliwy odczyt |  usuwa zawartość  | tworzy nowy |
| "a+" | dopisywanie + odczyt |  otwiera w trybie dopisywania + możliwy odczyt  |  dopisuje do końca  |  tworzy nowy |

Dodatkowo można użyć flagi ```b``` aby otworzyć plik w trybie binarnym.

---

##  Prosty przykład - wczytywanie

```c
#include <stdio.h>

int main() {
   char buffer[255];
   FILE *pF = fopen("poem.txt", "r");
   if(pF == NULL) {
      printf("Unable to open file!\n");
   }
   else {
      while(fgets(buffer, 255, pF) != NULL) {
         printf("%s", buffer);
      }
   }
   fclose(pF);

   return 0;
}
```

---

<!--
<style scoped>
ul {font-size: 26px;}
p  {font-size: 26px; margin-bottom: 6px;}
h3 {font-size: 28px; margin-bottom: 10px; margin-top: 16px;}
</style>
-->

##  ```fgets()```

```c
char* fgets(char* str, int count, FILE* stream);
```

 Wczytuje tekst ze strumienia aż do znaku ```\n``` lub wczytania ```count-1``` znaków (co nastąpi pierwsze) i zapisuje do ```str```. Funkcja kończy działanie również wtedy, gdy nie pozostało więcej danych. Wczytany wiersz jest zawsze zakończony znakiem  ```\0```. 

### Parametry
```str``` 	- wskaźnik do łańcucha tekstowego
```count``` 	- maksymalna liczba znaków do wczytania (zwykle długość ```str```)
```stream``` 	- strumień na którym ma zostać wykonana operacja

### Wartość zwracana

```str``` gdy operacja się udała, ```NULL``` gdy wystąpił błąd.

---

<!--
<style scoped>
ul { font-size: 26px; }
p  { font-size: 26px; margin-bottom: 6px;}
h3 { font-size: 28px; margin-bottom: 10px; margin-top: 16px;}
</style>
-->

##  ```fscanf()```

```c
int fscanf(FILE* stream, const char* format, ... );
```

Wczytuje dane ze strumienia ```stream```, interpretuje wczytane dane zgodnie z łańcuchem ```format``` i zapisuje w wskazanych lokalizacjach (argumenty po ```format```)

### Parametry
```stream``` - 	strumień, z którego mają być wczytane dane
```format``` - 	wskaźnik na łańcuch tekstowy, który określa jak wczytać dane (zakończony ```\0```)
```    ... ``` 	- 	argumenty, które przyjmują dane (przekazane przez wskaźnik)

### Wartość zwracana

Liczba argumentów, którym poprawnie nadano wartość (może wynosić ```0```, gdy błąd dopasowania wystąpił przed przypisaniem czegokolwiek pierwszemu argumentowi), lub wartość ```EOF```, jeśli błąd odczytu wystąpił przed przypisaniem czegokolwiek pierwszemu argumentowi.

---

## ```fgets()``` vs ```fscanf()```

 - ```fgets()``` czyta tekst do znaku nowej linii
 - ```fscanf()``` z *format specifier* ```%s``` wczytuje tekst do napotkania pierwszego białego znaku (takiego jak spacja, tab, znak nowej linii).

---

# Dostęp swobodny (*random access*)

- C umożliwia swobodny dostęp do plików - odczytawanie dowolnej liczby bajtów danych z dowolnego miejsca pliku
- funkcje ```fseek()``` i ```ftell()```

---

## ```ftell()```
```c
long ftell( FILE *stream );
```

Zwraca pozycję kursora w strumieniu ```stream```. Jeśli plik otwarto w trybie binarnym to zwraca liczbę bajtów od początku pliku. Jeśli w trybie tekstowym, to wartość nie powinna być interpretowana i może być tylko użyta jako parametr do ```fseek()```.

### Parameters

```stream```  -  strumień, na którym będzie przeprowadzana operacja

### Wartość zwracana

Aktualna pozycja kursora w przypadku sukcesu, ```-1L```  w przypadku błędu.

---

## ```fseek()```

```c
int fseek(FILE* stream, long offset, int origin);
```	
Ustawia nową wartość kursora dla podanego strumienia ```stream```.

### Jeśli plik jest otwarty w trybie binarnym

To nowa pozycja kursora odczytu/zapisu jest dokładnie przesunięta o ```offset``` bajtów licząc od:
- początku pliku (gdy ```origin``` to ```SEEK_SET```), 
- od bieżącej pozycji w pliku (gdy ```origin``` to ```SEEK_CUR```), 
- końca pliku (gdy ```origin``` to ```SEEK_END```). Strumienie binarne nie muszą obsługiwać ```SEEK_END```.

---

## ```fseek()```

```c
int fseek(FILE* stream, long offset, int origin);
```	
Ustawia nową wartość kursora dla podanego strumienia ```stream```.

### Jeśli plik jest otwarty w trybie tekstowym

To jedynymi obsługiwanymi wartościami parametru ```offset``` są ```0``` (które działa z dowolnym ```origin```) oraz wartość zwrócona przez wcześniejsze wywołanie ```ftell``` na strumieniu skojarzonym z tym samym plikiem (co działa wyłącznie z ```origin``` równym ```SEEK_SET```)

---

## ```fseek()``` - tryb tekstowy

### Dlaczego w trybie tekstowym używamy funkcji tylko w połączeniu z ```ftell```?

W trybie tekstowym biblioteka C może wykonywać dodatkowe konwersje znaków końca linii (np. między ```\n``` w kodzie a ```\r\n``` w pliku), co powoduje, że fizyczna liczba bajtów w pliku nie zawsze odpowiada liczbie znaków widzianych przez program.

```fseek``` z arbitralnym offset (np. ```fseek(stream, 10, SEEK_SET)```) mogłoby trafić w „środek” sekwencji końca linii i wywołać nieprzewidywalne zachowanie.

```ftell``` zwraca poprawną, spójną pozycję w pliku.

---

## ```fseek()```

<!--
<style scoped>
p { margin-bottom: 6px; }
h4{ font-size: 28px; margin-bottom: 10px; margin-top: 16px;}
</style>
-->

#### Parametry
```stream```	- 	strumień na którym ma zostać wykonana operacja,

```offset``` 	- 	wartość przesunięcia względem ```origin```,

```origin``` 	- 	pozycja, od której realizowane przesunięcie, możliwe wartości: 
  - ```SEEK_SET``` - względem początku pliku,
  - ```SEEK_CUR``` - względem aktualnej pozycji,
  - ```SEEK_END``` - względem końca pliku.

#### Wartość zwracana

​```0```​ gdy operacja się powiodła, w przeciwnym razie wartość różna od ```0```. 

---

## Przykład z ```fseek()``` i ```ftell()```

```c
#include <stdio.h>
#define CNTL_Z '\032'

int main() {
    FILE *pF = fopen("poem.txt", "r");
    char ch, buffer[255];
    if(pF == NULL) {
        printf("Unable to open file!\n");
    }
    else {
        long count, last;
        fseek(pF, 0L, SEEK_END);  // przesuwamy się na koniec pliku 
        last = ftell(pF);
        for (count = 1L; count <= last; count++) {
            fseek(pF, -count, SEEK_END); // idziemy wstecz 
            ch = getc(pF);
            if (ch != CNTL_Z && ch != '\r') putchar(ch);  // przepisujemy, ignorujemy znaki MS-DOS 
        } 
        putchar('\n'); 
    }
    fclose(pF);
    return 0;
}
```

---

## Uwagi

- Jeśli chcemy wymusić zapisanie zawartości bufora do pliku używamy ```fflush()```.
- Funkcje ```feof()``` i ```ferror()``` podają powód niepowodzenia operacji I/O.
- To, jak C interpretuje wejściowy lub wyjściowy strumień bajtów, zależy od tego, jakich funkcji wejścia/wyjścia używamy (bajty, tekst, tekstowa reprezentacja liczb).
- C rozróżnia zapisywanie danych i dołączanie (*appending*) danych. Kiedy dane są zapisywane do pliku, nadpisują to, co było tam wcześniej. Kiedy plik jest otwierany do dołączania, dane dodawane są na koniec, zachowując oryginalną zawartość pliku.

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
