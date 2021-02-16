RISCV_PGSIZE = 4096B
```c
#define ROUNDDOWN(a, b) ((a)/(b)*(b))
#define ROUNDUP(a, b) ((((a)-1)/(b)+1)*(b))
```
ROUNDDOWN is floor(a/b) x b
ROUNDUP is ceil(a/b) x b
ROUNDDOWN(5/2) = floor(2.5)x2 = 4
ROUNDUP(5/2) = ceil(2.5) x 2 = 6
