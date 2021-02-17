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
Hence, freelist_node_array_size = (16 x num_freelist_nodes) = 0x800 physical pages. The first page allocated is for the root_page_table.  
```c
root_page_table = 0x80017000 + (0x800 x 0x1000) = 0x80817000
```
The address of root_page_table is written into the sptbr csr.  

#### \_\_map_kernel_range  
In the virtual address space of a user process, addresses starting from 0x80000000 to 0x100000000 are reserved for kernel region. The user space is from 0x0 to 0x80000000. Hence we do a flat map (virtual addr = physical addr) for the kernel region. Also the kernel region page size is 2MB and not 4KB.
```c
root_page_table = 0x80817000
rpt_minus_1_t0 = 0x80818000    //rpt_minus_x_ty: x refers depth of tree. x = 0 refers to root.
rpt_minus_1_t1 = 0x80819000    //y refers to node number in level "x".
// rpt_minus_1_t0 & rpt_minus_1_t1 are leaf nodes in the page walk since we use 2MB pages.
// if the R,W,X bits in a pte entry are all 0 then it is a pointer pte node else it is a leaf node. (PTE_V should be set)
```
As of this point, the entire physical memory is mapped to the kernel.  

#### xv6 operating system  
On xv6, the kernel maps the entire physical memory into its address space starting at 0x80000000. Thus, virtual address (0x80000000+x) always translates to physical address x. Thus every page that is accessible by the user also has a mapping in the kernel's address space. Thus two entries in the page table point to the same physical page (one in the kernel-space, and another in the user-space).  
This is similarly followed in riscv-pk as well.

