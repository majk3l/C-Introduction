
# Podstawy programowania w języku C
### Wykład #6: Struktury. Biblioteka standardowa. 
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | 
-->

---
<!-- footer: | PPwJC | Wykład #6 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/
     page_number: true -->



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

- tworzenie zmiennej będącej strukturą podczas jej specyfikacji:

```
struct point2i {
   int x;
   int y;
} startPoint;
```

```startPoint``` jest strukturą typu ```point2i```.

---
# Struktury

- tworzenie zmiennej **bez nadawania identyfikatora typowi**:

```
struct {       // brak nadanej nazwy typu, po słowie 
   int x;      // kluczowy struct od razu występuje {
   int y;
} startPoint;
```

```startPoint``` jest strukturą, możemy jej używać, ale nie nazywając typu pozbawiamy się wygodnego sposobu na deklarowanie kolejnych takich struktur.

---


# Struktury

```c
struct point 
{ 
  int x = 0; //COMPILER ERROR: can't initialize members
  int y = 0; //COMPILER ERROR: can't initialize members
};  

```

---
# Struktury - wykorzystanie

### przykład: lista wskaźnikowa

```
 struct element {
   struct element *next; // wskaźnik na kolejny element
   int val;              // przechowywana wartość 
 };
 
```



---

# Biblioteka standardowa

### Dokonamy przeglądu kilku zagadnień/zadań/problemów pojawiających się podczas pisania programów (nie tylko w języku C).

### Zaprezentowane zostaną możliwości biblioteki standardowej, która dostarcza funkcjonalności, pomocnych w rozwiązywaniu powyższych.

---

# Biblioteka standardowa

---

# Przenośność kodu

- rozmiar typów zależny maszyny na której kompilujemy kod.

- chcemy pisać kod uniwersalny (byc może programujemy jakieś urządzenia *embedded*, albo fragmenty kodu działają na różnych platformach).

---

#  ```stdint.h``` oraz ```inttypes.h```

- tworzą dodatkowe aliasy dla typów,
- zapewniają stałą długość typu w bitach na róznych systemach.

### implementacja a interfejs:

- interfejs: otrzymujemy typy o zagwarantowanym rozmiarze;
- implementacja: rózna na róznych platformach.

---
#  ```stdint.h```


```c
int8_t        // signed integer type
int16_t       // with width of exactly
int32_t       // 8, 16, 32 and 64 bits respectively
int64_t       // with no padding bits and using 2's  
              // complement for negative values

int_fast8_t   // fastest signed integer type 
int_fast16_t  // with width of
int_fast32_t  // at least 8, 16, 32 and 64 bits 
int_fast64_t  // respectively


int_least8_t   // smallest signed integer type
int_least16_t  // with width of
int_least32_t  // at least 8, 16, 32 and 64 bits
int_least64_t  // respectively

// podobnie dla typów bez znaku: uint...
```
---

#  ```stdint.h```

#### Kiedy używać?

- piszemy kod na wiele platform
- robimy operacje na poszczególnych bitach, np. zapisujemy 4x 8 bitów w jednej zmiennej

---


#  ```stdint.h```

#### Kiedy używać?

<sup>
  
```c
// chcemy zapisać kolor (A,R,G,B) (255, 10, 255, 255)
// na zmiennej argb

int argb = ((255 << 24) | (10 << 16) | (255 << 8) | 255);

```

</sup>

---
#  ```stdint.h```

#### Kiedy używać?

<sup>
  
```c
// chcemy zapisać kolor (A,R,G,B) (255, 10, 255, 255)
// na zmiennej argb

int argb = ((255 << 24) | (10 << 16) | (255 << 8) | 255);

```
na architekturze x86 zadziała, rozmiar int powinnen wynosić 32 bit.

</sup>

---

---
#  ```stdint.h```

#### Kiedy używać?

<sup>
  
```c
// chcemy zapisać kolor (A,R,G,B) (255, 10, 255, 255)
// na zmiennej argb

int argb = ((255 << 24) | (10 << 16) | (255 << 8) | 255);

```
Microchip XC16: rozmiar int 16 bit, tu pojawia się problem.

</sup>

---

#  ```stdint.h```

#### Bezpieczniejszy kod

<sup>
  
```c
#include <stdint.h>

// chcemy zapisać kolor (A,R,G,B) (255, 10, 255, 255)
// na zmiennej argb

uint32_t argb = ((255 << 24) | (10 << 16) | (255 << 8) | 255);

```

</sup>

---

# Dygresja

## zadanie dla dociekliwych:

jak odczytać poszczególne kanały (A,R,G,B) ze zmiennej ```uint32_t argb```, która przechowuje wartości kolorów w ten sposób?

```
 31                                   0
  |AAAAAAAA|RRRRRRRR|GGGGGGGG|BBBBBBBB|

```
Używamy operacji bitowych, podobnie jak przy zapisie.

---

# Operacje na znakach

Operujemy na znakach i chcemy wygodnie wykonywać na nich podstawowe operacje, np.:

- sprawdzić czy są literami, cyframi, wielkimi literami, itd.
- modyfikować je (np. konwertować na małe znaki).

---

# ```ctype.h```


*The ctype.h header file of the C Standard Library declares several functions that are useful for testing and mapping characters.*

--- 

# ```ctype.h``` - przykład

```c
int isalpha ( int c );
```

*Checks whether c is an alphabetic letter.*

**param:**
&nbsp;&nbsp; *Character to be checked, casted to an int, or EOF.*

**return value:**

&nbsp;&nbsp; *A value different from zero (i.e., true) if indeed c is an alphabetic letter. Zero (i.e., false) otherwise.*

---
# ```cfloat.h```

### Characteristics of floating-point types

*This header describes the characteristics of floating types for the specific system and compiler implementation used.*

---
# ```cfloat.h``` - rzeczy warte uwagi

### Precyzja obliczeń zmiennoprzecinkowych!

*squeezing infinitely many real numbers into a finite number of bits requires an approximate representation.*


```FLT_EPSILON``` - *Difference between 1 and the least value greater than 1 that is representable.*


---
# ```FLT_EPSILON``` 


```
float a, b;
//...
if(a == b) {   /// <- tak nigdy nie robimy!
//...
}

```

---
# ```FLT_EPSILON```  

```
float a, b;
//...
if(fabs(a - b) < EPSILON) {   /// <- tak jest lepiej
//...
}

```

czy ```EPSILON``` powinien być równy ```FLT_EPSILON``` ?

Przy wartościach w przedziale [-2 2] tak, dla liczb o większej wartości bezwzględnej niż 2 trzeba go przeskalować.

---

# Epsilon maszynowy...

##  ... dla  dociekliwych:


>David Goldberg. 1991. What every computer scientist should know about floating-point arithmetic. ACM Comput. Surv. 23,5-48. DOI: https://doi.org/10.1145/103162.103163 

---

# Konwersje do formatów numerycznych

Wczytaliśmy dane do łańcucha tekstowego, potrzebujemy zamienić je na liczby (np całkowite).


---

# ```<stdlib.h>```

&nbsp;
```
atoi()

atof()
```

---
# ```atoi()```

```c

int       atoi( const char *str );
		
long      atol( const char *str );
		
long long atoll( const char *str );

```

*Interprets an integer/long/long long value in a byte string pointed to by str.*


---
# ```atoi()```

```
int atoi( const char *str );
```

- *discards any whitespace characters until the first non-whitespace character is found*  
- *takes as many characters as possible to form a valid integer number representation and converts them to an integer value.*
- *the valid  value consists of the following parts:*
  - *plus or minus sign (optional)*
  - *numeric digits*


---

# ```atoi()```

```
int atoi( const char *str );
```
**Parameters:**
```str``` - *pointer to the null-terminated byte string to be interpreted*
**Return value:**
*Integer value corresponding to the contents of str on success. If the converted value falls out of range of corresponding return type, the return value is undefined. If no conversion can be performed, ​0​ is returned.*


---
# ```atof()```

Działa na podobnej zasadzie co ```atoi()```, rozpatruje kilka notacji liczb zmiennoprzecinkowych, ```nan``` oraz ```inf```


---
# ```inf``` oraz ```nan```

```nan``` - reprezentuje wynik nie mający interpretacji liczbowej.
```inf``` - *an expression representing positive infinity. It is equal to the value produced by mathematical operations like 1.0 / 0.0.*


---
# ```string.h```

strcpy

strncpy

strlen

strcmp

strncmp

strchr

strrchr

strstr

---

# ```strcpy()```


---


