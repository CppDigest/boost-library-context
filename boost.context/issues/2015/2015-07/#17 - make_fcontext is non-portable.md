# #17 - make_fcontext is non-portable. [Closed]

> Username: penimi  
> Created at: 2015-07-05 09:51:21 UTC  
> Updated at: 2016-01-22 19:22:41 UTC  
> Closed at: 2016-01-22 19:22:41 UTC  
> Url: https://github.com/boostorg/context/issues/17  

The example given in the Boost documentation gives make_fcontext as taking a block of heap memory for the new stack. In the example, pointer from malloc is given directly to make_fcontext. However, the behaviour of make_fcontext is architecture dependent. It either expects a pointer to the bottom of the stack (consistent with the result of malloc) or the top of the stack (which requires pointer arithmetic to derive the end of the malloc block)  
  
Code that uses this interface is therefore non-portable. Since the size of the stack is always passed to make_fcontext it would be trivial to update the assembler implementation to ALWAYS expect a pointer to the bottom of the stack, making interfacing code portable again.

---

## Comment 1

> Username: olk  
> Created at: 2016-01-22 19:22:41 UTC  
> Url: https://github.com/boostorg/context/issues/17#issuecomment-174017744  

stack is managed by stackallocators like fixedsize_stack - fcontext-API is now an implementation detail
