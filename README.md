Riscv supports different page sizes like 4KB,2MB etc. Pages of both sizes can be simultaneously present.
```c
#define ROUNDDOWN(a, b) ((a)/(b)*(b))              //floor(a/b) x b
#define ROUNDUP(a, b) ((((a)-1)/(b)+1)*(b))        //ceil(a/b) x b
```
