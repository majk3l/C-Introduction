# Podstawy programowania w języku C
### Wykład #7: Operacje na plikach
*dr inż. Michał Kępski* 

---

# ```<stdio.h>```

```
fopen(), getc(), putc(), exit(), fclose(), fprintf(),
fscanf(), fgets(), fputs() rewind(), fseek(), ftell(),
fflush(), fgetpos(), fsetpos(), feof(), ferror() ungetc(),
setvbuf(), fread(), fwrite()
```
---

# Czym jest plik?

- Plik jest nazwaną sekcją pamięci trwałej, służy do przechowywania danych.
- Z poziomu języka C jest on ciągłą sekwencją bajtów, z której każdy może zostać osobno odczytany.

---
# Pliki binarne a pliki tekstowe

- Cała zawartość pliku jest w formie binarnej (zera i jedynki). 

- Jeśli plik używa kodów binarnych dla znaków (ASCII lub Unicode) do reprezentacji tekstu, to jest to plik o zawartości tekstowej.

- Jeśli wartości binarne reprezentują kod języka maszynowego, dane numeryczne czy kodowanie obrazu lub muzyki, to mówimy, że zawartość pliku jest binarna.

---
# Pliki binarne a pliki tekstowe

- Różne systemy używają różnych kodów do oznaczenia końca linii - Linux i MacOS ```\n``` a MS Dos czy Windows ```\r\n```.

- Jeśli plik tekstowy zostaje otwarty w trybie tekstowym to biblioteka C zapewnia konwersję tych symboli do ```\n```.

- Jeśli plik tekstowy otworzymy w trybie binarnym, dostaniemy takie symbole jakie zostały zapisane (nie ma żadnej konwersji).

---
# Buforowanie

- Zawartość pliku nie jest wczytywana bajt po bajcie, lecz kopiowana do tymczasowego miejsca w pamięci, zwanego buforem.
- Pozwala to znacznie przyśpieszyć operacje I/O.
- Poszczególne elementy bufora mogą być pojedynczo analizowane.

---
# Pliki – podstawowe informacje

- C postrzega każdy plik jako sekwencyjny strumień bajtów. 
- Każdy plik kończy się albo znacznikiem końca pliku, albo określonym numerem bajtu zapisanym w systemowej administracyjnej strukturze danych. 
- Gdy plik jest otwierany, kojarzony jest z nim strumień. 
- 3 pliki i związane z nimi strumienie są automatycznie otwierane podczas uruchomienia programu - standardowe wejście (```stdin```), standardowe wyjście (```stdout```) i standardowy błąd (```stderr```).

---
# ```fopen()```

- Funkcja zadeklarowana w ```stdio.```.

- Pierwszy argument to nazwa pliku do otwarcia, drugi argument to łańcuch określający tryb, w jakim plik ma być otwarty. 

- Po pomyślnym otwarciu pliku ```fopen()``` zwraca wskaźnik na plik, w przeciwnym razie zwraca pusty wskaźnik.

- Funkcje I/O mogą następnie użyć wskaźnika do operacji na pliku, wskaźnik jest typu ```FILE*```, a ```FILE``` jest typem pochodnym zdefiniowanym w ```stdio.h```.

- Wskaźnik nie wskazuje na rzeczywisty plik, lecz na obiekt danych zawierający informacje o pliku oraz informacje o buforze.

--- 
# Wczytywanie po znaku: ```getc()```

```c
int getc(FILE* stream);
```

Reads the next character from the given input stream.

### Parameters
```stream``` - to read the character from 

### Return value

On success, returns the obtained character as an ```unsigned char converted``` to an ```int```. On failure, returns ```EOF```.

If the failure has been caused by end-of-file condition, additionally sets the *eof* indicator (see ```feof()```) on stream. If the failure has been caused by some other error, sets the error indicator (see ```ferror()```) on stream. 

---
# End-of-file

- Program odczytujący dane musi się zatrzymać po osiągnięciu końca pliku. 
- Funkcja ```getc()``` zwraca specjalną wartość ```EOF```, jeśli próbuje odczytać jakiś znak i odkrywa, że dotarła do końca pliku.

- Aby uniknąć próby odczytania z pliku po osiągnięciu jego końca, można stosować przykładowo następujący warunek pętli:

```c
while (( ch = getc(fp)) != EOF)
```

---
# ```fclose()```

- Zamyka plik określony przez parametr (wskaźnik do pliku).
- W razie potrzeby opróżnia wcześniej bufor (operacja *flush*). 
- Zwraca wartość ```0``` jeśli zamknięcie pliku się udało, w przeciwnym razie zwraca ```EOF```.

---
#  Prosty przykład - zapisywanie

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
# ```fprintf()```

```c
int fprintf(FILE* stream, const char *format, ...);
```

Loads the data from the given locations, converts them to character string equivalents and writes the results to output stream ```stream```.

### Parameters
```stream``` - to read the character from 
```format``` 	- 	pointer to a null-terminated multibyte string specifying how to interpret the data
```...``` 	- 	arguments specifying data to print.

### Return value

Number of characters transmitted to the output stream or negative value if an output error or an encoding error (for string and character conversion specifiers) occurred.

---
#  Mode strings

|  Code  |  Meaning  |  Explanation  |  Action if file   already exists  |  Action if file   does not exist  |
|:---:|:---:|:---:|:---:|:---:|
|  "r"  |  read  |  Open a file for reading  |  read from start  |  failure to open  |
|  "w"  |  write  |  Create a file for writing  |  destroy contents  |  create new  |
|  "a"  |  append  |  Append to a file  |  write to end  |  create new  |
|  "r+"  |  read extended  |  Open a file for read/write  |  read from start  |  error  |
|  "w+"  |  write extended  |  Create a file for read/write  |  destroy contents  |  create new  |
|  "a+"  |  append extended  |  Open a file for read/write  |  write to end  |  create new  |

File access mode flag "b" can optionally be specified to open a file in binary mode.

---
#  Prosty przykład - wczytywanie

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
#  ```fgets()```

```c
char* fgets(char* str, int count, FILE* stream);
```

Reads at most ```count - 1``` characters from the given file stream and stores them in the character array pointed to by ```str```. Parsing stops if a newline character is found, in which case str will contain that newline character, or if ```EOF``` occurs. If bytes are read and no errors occur, writes a null character at the position immediately after the last character written to ```str```.

### Parameters
- ```str``` 	- pointer to an element of a char array
- ```count``` 	- maximum number of characters to write (typically the length of ```str```)
- ```stream``` 	- file stream to read the data from

### Return value

str on success, null pointer on failure. 

---
#  ```fscanf()```

```c
int fscanf(FILE* stream, const char* format, ... );
```

Reads data from file stream ```stream```, interprets it according to format and stores the results into given locations.

Parameters
```stream``` - 	input file stream to read from
```format``` - 	pointer to a null-terminated character string specifying how to read the input.
```...``` 	- 	receiving arguments. 

### Return value

Number of receiving arguments successfully assigned (which may be zero in case a matching failure occurred before the first receiving argument was assigned), or ```EOF``` if input failure occurs before the first receiving argument was assigned.

---
# ```fgets()``` vs ```fscanf()```

 - ```fgets()``` czyta tekst do znaku nowej linii
 - ```fscanf()``` z *format specifier* ```%s``` wczytuje tekst do napotkania pierwszego białego znaku (takiego jak spacja, tab, znak nowej linii).

---
# Dostęp swobodny (*random access*)

- C umożliwia swobodny dostęp do plików - odczytawanie dowolnej liczby bajtów danych z dowolnego miejsca pliku
- funkcje ```fseek()``` i ```ftell()```

---
# ```fseek()```

```c
int fseek(FILE* stream, long offset, int origin);
```	
Sets the file position indicator for the file stream ```stream``` to the value pointed to by ```offset```. If the stream is open in binary mode, the new position is exactly ```offset``` bytes measured from the beginning of the file if ```origin``` is ```SEEK_SET```, from the current file position if ```origin``` is ```SEEK_CUR```, and from the end of the file if ```origin``` is ```SEEK_END```. Binary streams are not required to support ```SEEK_END```. If the stream is open in text mode, the only supported values for ```offset``` are zero and a value returned by an earlier call to ```ftell```.

### Parameters
```stream```	- 	file stream to modify
```offset``` 	- 	number of characters to shift the position relative to ```origin```
```origin``` 	- 	position to which offset is added, one of the following values: ```SEEK_SET```, ```SEEK_CUR```, ```SEEK_END```

### Return value

​0​ upon success, nonzero value otherwise. 

---
# ```ftell()```
```c
long ftell( FILE *stream );
```

Returns the file position indicator for the file stream ```stream```. If the stream is open in binary mode, the value obtained by this function is the number of bytes from the beginning of the file. If the stream is open in text mode, the value returned by this function is unspecified and is only meaningful as the input to ```fseek()```.

### Parameters
```stream```  -  file stream to examine

### Return value

File position indicator on success or ```-1L``` if failure occurs.

On error, the ```errno``` variable is set to implementation-defined positive value. 
		
---
# Przykład z ```fseek``` i ```ftell```

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
        fseek(pF, 0L, SEEK_END); /* go to end of file */ 
        last = ftell(pF);
        for (count = 1L; count <= last; count++) {
            fseek(pF, -count, SEEK_END); /* go backward */ 
            ch = getc(pF);
            if (ch != CNTL_Z && ch != '\r') { /* MS-DOS files */
                putchar(ch); 
            }
        } 
        putchar('\n'); 
    }
    fclose(pF);
    return 0;
}
```
---
# Uwagi

- Jeśli chcemy wymusić zapisanie zawartości bufora do pliku używamy ```fflush()```.
- Funkcje ```feof()``` i ```ferror()``` podają powód niepowodzenia operacji I/O.
- To, jak C interpretuje wejściowy lub wyjściowy strumień bajtów, zależy od tego, jakich funkcji wejścia/wyjścia używamy (bajty, tekst, tekstowa reprezentacja liczb).
- C rozróżnia zapisywanie danych i dołączanie (*appending*) danych. Kiedy dane są zapisywane do pliku, nadpisują to, co było tam wcześniej. Kiedy plik jest otwierany do dołączania, dane dodawane są na koniec, zachowując oryginalną zawartość pliku.

---
# Bibliografia

1. Stephen Prata. 2013. C Primer Plus (6th Edition) (6th. ed.). Addison-Wesley Professional.
2. https://en.cppreference.com/w/c
3. Standard języka C (ISO/IEC 9899:TC3): https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf 
