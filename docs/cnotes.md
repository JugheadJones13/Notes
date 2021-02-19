### C and C++  
The nm command gives the symbol table.
```bash
nm a.out
```
##### Global static variables  
Static keyword restricts the scope of a variable to the file it is defined in. By default global variables are extern, meaning I can define a global variable in A.c and then say "extern var" in B.c and use it in B.c. Same applies to static functions as well.
##### Bitwise and logical operators
"& | ^ ~" are bitwise operators while "&& || !" are logical operators
```c
0b1000 && 0b1000 = 0b0001
0b1000 & 0b1000 = 0b1000
```
##### left shift count >= width of type
```c
uint64_t a = (1 << 35);     //uint64_t in stdint.h, 1 is 32 bit int. 1 << 35 cannot be stored (overflow).
uint64_t a = (1UL << 35);   //this works
```
##### GDB
```
set print elements 0 //Display full string
```
