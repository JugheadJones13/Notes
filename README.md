Riscv supports different page sizes like 4KB,2MB etc. Pages of both sizes can be simultaneously present.
```c
#define ROUNDDOWN(a, b) ((a)/(b)*(b))              //floor(a/b) x b
#define ROUNDUP(a, b) ((((a)-1)/(b)+1)*(b))        //ceil(a/b) x b
```
DRAM start address is 0x80000000. DRAM is 2GB in size.

#### Global static variables  
Static keyword restricts the scope of a variable to the file it is defined in. By default global variables are extern, meaning I can define a global variable in A.c and then say "extern var" in B.c and use it in B.c. Same applies to static functions as well.
