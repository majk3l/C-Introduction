
# Podstawy programowania w języku C
### Wykład #2: Struktura programu. Typy danych, operatory, funkcje.
*dr inż. Michał Kępski* 

<!-- footer: | e-mail: mkepski@ur.edu.pl | 
-->

---
<!-- footer: | PPwJC | Wykład #2 | dr inż. Michał Kępski | https://github.com/majk3l/C-Introduction/
     page_number: true -->

# Struktura programu

##### Przykładowy program:

```c
#include <stdio.h> /* dołączenie pliku nagłówkowego */

int main() 
{
    int year; /* deklaracja zmiennej */
    year = 2018; /* przypisanie wartości do zmiennej */
    
    /* To jest komentarz 
    zawierający więcej niż jedną linię */
    
    // To również jest komentarz, ale standardu C99
    printf("Hello in %d\n", year); // wywołanie funkcji
    
    return 0;
}
```

---

# Struktura programu


  
```shell                          
+-------------+ 
| Program w C |   
+-------------+
|    +-----------+
+----|#include...|   <------dyrektywy preprocesora
|    +-----------+ 
|    +-----------+   
+----|int main() |   <------funkcja główna
|    +-----------+
|              |          +------------+      zawierają
|              +----------| instrukcje |  ________________
|                         +------------+                  |       
|                                   |                     V
|    +-----------+    funkcje       |                 +--------------+ 
+----|funkcja a()|  składają się    :deklaracje     <-|słowa kluczowe|
|    +-----------+  z instrukcji    :przypisania    <-|identyfikatory|
|              |  +------------+    :funkcje        <-|   literały   |
|              +--| instrukcje |    :sterujące      <-|  operatory   |
|                 +------------+    :puste          <-|  separatory  | 
|    +-----------+           |          ^           <-+--------------+                
+----|funkcja b()|          ...        /|\                        
|    +-----------+                      |                           
V                                rodzaje instrukcji    
```


---

# ```#include``` 

- dyrektywa preprocesora
- przetwarzana przed procesem kompilowania kodu
- musi rozpoczynać się znakiem #

```c
#define EMPTY
EMPTY # include <file.h>
```
druga linia listingu **nie zawiera** dyrektywy preprocesora!

---

# Funkcja ```main()```

- funkcja od której program rozpoczyna działanie, 

- jej istnienie jest wymagane,

- zazwyczaj zawiera wywołania innych funkcji.


---

---
# Struktura programu

##### Przykładowy program:

```c
#include <stdio.h> /* dołączenie pliku nagłówkowego */

int main() 
{
    int year; /* deklaracja zmiennej */
    year = 2018; /* przypisanie wartości do zmiennej */
    
    /* To jest komentarz 
    zawierający więcej niż jedną linię */
    
    // To również jest komentarz, ale standardu C99
    printf("Hello in %d \n", year); /* wywołanie funkcji */
    
    return 0;
}
```
---
# Słowa kluczowe

Zarezerwowane elementy składni języka, nie można ich użyć do innych celów niż przeznaczone (np. jako nazwy własnych zmiennych).

```auto``` ```break``` ```case``` ```char``` ```const``` ```continue``` ```default``` ```do``` 
```double``` ```else``` ```enum``` ```extern``` ```float``` ```for``` ```goto``` ```if``` ```int```
```long``` ```register``` ```return``` ```short``` ```signed``` ```sizeof``` ```static```
```struct``` ```switch``` ```typedef``` ```union``` ```unsigned``` ```void``` 
```volatile``` ```while``` **```inline``` ```_Bool``` ```_Complex``` ```_Imaginary```**

---
# Literały

Stałe wartości w kodzie źródłowym, przykładowo ```5``` czy ```'e'```.

Mają określony typ, określany przez kompilator na podstawie ich wartości. Można też wskazać typ przez rzutowanie.

---
# Literały znakowe *escape sequences*

- składają się z dwóch lub więcej znaków
- mapują się do pojedynczej wartości typu ```char```
- reprezentują znaki specjalne i niedrukowane elementy tekstu (znaki nowej linii, tabulacje, etc.)
- przykłady: ```'\n' '\t' '\\'```



---
# Wyrażenia


Wyrażenia składają się z **operatorów i operandów.** Pojedynczy operand także jest wyrażeniem.

Wyrażenia mają wartości.

---
# Wyrażenia

wyrażenie: &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; wartość:

```-4 + 6```    &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;    2

```c = 3 + 8``` &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;                        11

```5 > 3``` &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1

```6 + (c = 3 + 8)``` &nbsp;                                                      17

---
# Operatory

Wartości wyrażeń wyznaczane są w oparciu o:
- semantykę,
- priorytet,
- łączność

operatorów, oraz wartości operandów.

---
# Typy operatorów

- *Arithmetic* (arytmetyczne, w tym bitowe)
- *Assignment* (przypisania)
- *Member access* (dostępu)
- *Logical* (logiczne)
- *Comparison* (porównania)

---
# Arytmetyczne
  
| Operator | Nazwa| Przykład | Rezultat|
|:-:|:-:|:-:|:- |
| ```+```  | plus (unarny)  | ```+a```    | promocja typu do ```int```  |
| ```-```  | minus (unarny) | ```-a```   | zmiana znaku na przeciwny |
| ```+```  | dodawanie | ```a + b```   | suma wartości operandów |
| ```-```  | odejmowanie | ```a - b``` | różnica wartości operandów |
| ```*```  | mnożenie | ```a * b```   | iloczyn wartości operandów |
| ```/```  | dzielenie | ```a / b```  | iloraz wartości operandów |
| ```%```  | modulo | ```a % b```   | resza z dzielenia ```a``` przez ```b``` |

---

# Arytmetyczne: bitowe

| Operator | Nazwa| Przykład | Rezultat|
|:-:|:-:|:-:|:- |
| ```~```  | negacja bitowa      | ```~a```   | negacja (dopełnienie) bitowe  |
| ```&```  | iloczyn bitowy      | ```a & b```| iloczyn bitowy ```a``` i ```b```  |
| ```\|```  | suma bitowa         | ```a \| b```  | suma bitowa ```a``` i ```b``` |
| ```^```  | xor  | ```a ^ b```   |  |
| ```<<``` | przesunięcie bitowe | ```a << b```   | ```a``` przesunięte o ```b``` bitów w lewo |
| ```>>``` | przesunięcie bitowe | ```a >> b```   | ```a``` przesunięte o ```b``` bitów w prawo |

---

# Przypisania

 
| Operator | Nazwa| Przykład | Rezultat|
|:-:|:-:|:-:|:- |
| ```=```   | *basic assignment*       | ```a = b```    | ```a``` otrzymuje wartość ```b``` |
| ```+=```  | *addition assign.*    | ```a += b```   | ```a``` otrzymuje wartość ```a + b``` |
| ```-=```  | *subtraction assign.* | ```a -= b```   | ```a``` otrzymuje wartość ```a - b``` |
| ```*=```  | *multiplication assign.* | ```a *= b```   | ```a``` otrzymuje wartość ```a * b``` |
| ```/=```  | *division assign.* | ```a /= b```   | ```a``` otrzymuje wartość ```a / b``` |
| ```%=```  | *modulo assign.* | ```a %= b```   | ```a``` otrzymuje wartość  ```a % b``` |
| ```&=```  | *bitwise AND assign.* | ```a &= b```   | ```a``` otrzymuje wartość ```a & b``` |

Istnieją także operatory przypisania dla pozostałych operacji bitowych ```|=``` ```^=``` ```<<=``` ```>>=```, działają one anlogicznie do ```&=```.

---

# Logiczne
&nbsp;
<sup>
  
| Operator | Nazwa| Przykład | Rezultat|
|:-:|:-:|:-:|:- |
| ```!```   | negacja logiczna | ```!a```       | logiczna negacja ```a```|
| ```&&```  | iloczyn logiczny | ```a && b```   | logiczny iloczyn ```a``` i ```b``` |
| ```\|\|```  | suma logiczna    | ```a || b```   | logiczna suma  ```a``` i ```b```   |

---
# Logiczne: NOT

*The logical NOT expression has the form*

```!expression```		

*where:*

***expression***	-	*an expression of any scalar type.*

*The logical NOT operator has type* **```int```**. *Its value is* ``0`` *if expression evaluates to a value that compares unequal to zero.*

*Its value is* ```1``` *if expression evaluates to a value that compares equal to zero.*

---

# Logiczne: AND

*The logical AND expression has the form*

```lhs && rhs```		

*where*

***lhs, rhs*** - an expression of any scalar type

*The logical AND operator has type* **```int```** *and the value* ```1``` *if both lhs and rhs compare unequal to zero. It has the value* ```0``` *otherwise (if either lhs or rhs or both compare equal to zero).*

*There is a sequence point after the evaluation of lhs. If the result of lhs compares equal to zero, then rhs is not evaluated at all (so-called short-circuit evaluation).*

---
# Comparison (porównania)
  
| Operator | Przykład | Rezultat|
|:-:|:-:|:-:|
|```==```| ```a == b```|1 jeśli wartości ```a``` i ```b``` są równe, inaczej 0|
|```!=```| ```a != b```|1 jeśli wartości ```a``` i ```b``` są różne, inaczej 0|
|```<``` |```a < b```|1 jeśli wartość ```a``` mniejsza od  ```b```, inaczej 0|
|```>```|```a > b```|1 jeśli wartość ```a``` większa od  ```b```, inaczej 0|
|```<=```| ```a >= b```|1 jeśli wartość ```a``` mniejsza lub równa od  ```b```, inaczej 0|
|```>=```|```a >= b```|1 jeśli wartość ```a``` większa lub równa od  ```b```, inaczej 0|

---

# Operatory

- **priorytet** - w jakiej kolejności wykonywane będą różne operacje zawarte w tym samym wyrażeniu
- **łączność** - kolejność wykonywania operacji o tym samym priorytecie

---

# Operatory: priorytet i łączność

https://en.cppreference.com/w/c/language/operator_precedence

---

# Instrukcje

Podstawowe elementy budowy programu. Instrukcje zakończone są średnikiem.

```eyes = 2```
jest wyrażeniem, ale
```eyes = 2;```
jest instrukcją.

---
# Instrukcje

- ;  (instrukcja pusta)
- proste 
- złożone

---
# Instrukcje złożone

Zawierają jedną lub więcej instrukcji (które też mogą być złożone) i są otoczone znakami ```{}```
