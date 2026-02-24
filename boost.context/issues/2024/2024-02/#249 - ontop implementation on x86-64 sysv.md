# #249 - ontop implementation on x86-64 sysv [Closed]

> Username: fafok29  
> Created at: 2024-02-05 13:04:07 UTC  
> Updated at: 2024-02-06 21:01:13 UTC  
> Closed at: 2024-02-06 21:01:13 UTC  
> Url: https://github.com/boostorg/context/issues/249  

I'm trying to use ontop_fcontext from C, the problem is whenever I call any function inside ontop_cb, I get garbage inside of transfer_t argument of fiber cb.  
  
at the end of ontop_fcontext we set %rdi (and %rsi) to fiiber ctx ptr and user ptr which is transfer_t after that we jump into ontop_cb  
  
https://github.com/boostorg/context/blob/e9c3e56a334a86966f3b807565afcbb157413c11/src/asm/ontop_x86_64_sysv_elf_gas.S#L129-L134  
  
ontop_cb expects transfer_t to be inside %rdi %rsi which is according to sysv, and fine, what I don't understand  is according to sysv we don't have to preserve %rdi %rsi, so in case we call anything from ontop_cb %rdi and %rsi will be overwritten  
  
```  
test_ontop_fcontext_cb:  
    push %rbp  
    movq %rsp, %rbp  
    sub $0x20, %rsp  
  
    // store transfer_t on stack  
    movq %rdi, -0x20(%rbp)  
    movq %rsi, -0x18(%rbp)  
  
    xor %rax, %rax  
  
    // rdi/rsi overwritten  
    movq %r11, %rdi  
    movq %rax, %rsi  
    movq %rax, %rdx  
      
    // call of C func with 3 args  
    call test_func  
      
    //return transfer_t  
    movq -0x20(%rbp), %rax  
    movq -0x18(%rbp), %rdx  
  
    addq $0x20, %rsp  
    pop %rbp  
    ret //jump into trampoline from make_fcontext  
  
    trampoline:  
    push %rbp  
    jmp *%rbx //jump to fiber_cb, with no additional setup  
```  
  
After the jump fiber_cb expects transfer_t to be inside %rdi %rsi which is not happening because we overwritten them.  
Also, why ontop_cb return transfer_t if it is not going to be used anyway (%rax %rdx used for return), but fiber_cb expects transfer_t in %rdi %rsi.  
  
I can write an asm function that preserves %rdi %rsi, but seems like the C function also should work in this case.
