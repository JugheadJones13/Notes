This page is a writeup of some of the stuff I learnt from reading the [riscv-pk](https://github.com/riscv/riscv-pk) repository. This writeup is meant as a journal of what I have learnt and it does not guarantee correctness. I suggest going through the original code to clarify any queries that may arise.

Riscv supports different page sizes like 4KB,2MB etc. Pages of both sizes can be simultaneously present.
```c
#define ROUNDDOWN(a, b) ((a)/(b)*(b))              //floor(a/b) x b
#define ROUNDUP(a, b) ((((a)-1)/(b)+1)*(b))        //ceil(a/b) x b
```
#### Global static variables  
Static keyword restricts the scope of a variable to the file it is defined in. By default global variables are extern, meaning I can define a global variable in A.c and then say "extern var" in B.c and use it in B.c. Same applies to static functions as well.

#### pk_vm_init()  
All \*.c files are compiled to \*.o files which are linked to produce the "pk" executable. \&\_end is the address of the end of all sections in pk executable. The max address in the final executable is 0x800160d8. ROUNDUP(0x800160d8,RISCV_PGSIZE) gives 0x80017000 which is the address of \_end.
```asm
riscv64-unknown-elf-objdump -D pk | less
```
So, the first_free_page = 0x80017000. DRAM start address is 0x80000000. DRAM is 2GB in size. Hence there are a total of 2GB/4KB physical frames which is represented by num_freelist_nodes.  
```c
sizeof(struct freelist_node_t) = 16; //in bytes.
```  
Hence freelist_node_array_size = (16 x num_freelist_nodes) = 0x800 physical pages.
