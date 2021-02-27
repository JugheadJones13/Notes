### ucontext.h
ucontext_t is used to create threads. ucontext_t is a struct defined in ucontext.h It has various methods such as:
* [getcontext](https://man7.org/linux/man-pages/man3/getcontext.3.html): initializes the structure pointed to by ucp (in arg list) to the currently active context
* [setcontext](https://man7.org/linux/man-pages/man3/getcontext.3.html): restores the user context pointed to by ucp.  A successful call does not return.
* [makecontext](https://man7.org/linux/man-pages/man3/makecontext.3.html): modifies the context pointed to by ucp. the caller must allocate a new stack for
  this context and assign its address to "ucp->uc_stack", and define a successor context and assign its address to "ucp->uc_link"
* [swapcontext](https://man7.org/linux/man-pages/man3/makecontext.3.html): saves the current context in the
  structure pointed to by oucp, and then activates the context pointed to by ucp.
  
  
The following is a simple example using ucontext.  
```C
#include <stdio.h>
#include <stdlib.h>
#include <ucontext.h>

ucontext_t A_context,B_context,main_context;

void printA(){
   printf("This is A\n");
   swapcontext(&A_context,&B_context);
}


void printB(){
   printf("This is B\n");
   swapcontext(&B_context,&A_context);
}

int main(){
   char Astk[1000];
   char Bstk[1000];
   getcontext(&main_context);
   getcontext(&A_context);
   A_context.uc_link = &main_context;
   A_context.uc_stack.ss_sp = Astk;
   A_context.uc_stack.ss_size = 1000;
   makecontext(&A_context,printA,0);
   getcontext(&B_context);
   B_context.uc_link = &main_context;
   B_context.uc_stack.ss_sp = Bstk;
   B_context.uc_stack.ss_size = 1000;
   makecontext(&B_context,printB,0);
   swapcontext(&main_context,&A_context);
   return 0;
}
```

References:  
[https://man7.org/linux/man-pages/man3/makecontext.3.html](https://man7.org/linux/man-pages/man3/makecontext.3.html)
[https://en.wikipedia.org/wiki/Setcontext](https://en.wikipedia.org/wiki/Setcontext)
