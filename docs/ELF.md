### Relocation in object files
Individual steps in the compilation process are:
1. Preprocessor: expands macros, directives etc (like #define)
2. Compiler: compiles the source code into assembly file (.s file)
3. Assembler: creates object file from .s files.
4. Linker: Takes obj file and returns executable.  
```c
long global_symbol[2];

int main() {
  return global_symbol[0] != 0;
}
```
In the above example during assembler stage, the actual address of 'global_symbol' is not known. 
The assembler inserts an entry for this in the relocation table. The linker checks the relocation table and fills in the address when it is resolved.
Dynamic linking is further defered until the program is actually run.
```asm
$ riscv64-unknown-linux-gnu-objdump -d -t -r relocation.o

relocation.o:     file format elf64-littleriscv

SYMBOL TABLE:
...
0000000000000010       O *COM*  0000000000000008 global_symbol

Disassembly of section .text.startup:

0000000000000000 main:
   0:   000007b7                lui     a5,0x0
                        0: R_RISCV_HI20 global_symbol
                        0: R_RISCV_RELAX        *ABS*
   4:   0007b503                ld      a0,0(a5) # 0 main
                        4: R_RISCV_LO12_I       global_symbol
                        4: R_RISCV_RELAX        *ABS*
   8:   00a03533                snez    a0,a0
   c:   8082                    ret
```
In the above objdump, the assembler has inserted relocations at 0 and 4. R_RISCV_HI20 means, replace the 0x0 with the upper 20 bits of global_symbol label once its 
address is resolved (in this case during the linking). The resolved state is shown below:
```asm
$ riscv64-unknown-linux-gnu-objdump -d -t -r relocation
relocation:     file format elf64-littleriscv

SYMBOL TABLE:
0000000000012038 g     O .bss 0000000000000010              global_symbol
...

Disassembly of section .text:

0000000000010330 main:
 10330:       67c9                    lui     a5,0x12
 10332:       0387b503                ld      a0,56(a5) # 12038 global_symbol
 10336:       00a03533                snez    a0,a0
 1033a:       8082                    ret
```

### References
* [https://www.sifive.com/blog/all-aboard-part-2-relocations](https://www.sifive.com/blog/all-aboard-part-2-relocations)
