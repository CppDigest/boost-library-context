# #278 - add an ability to execute a callback function on behalf of target coroutine stack right before return to target coroutine [Open]

> Username: microcai  
> Created at: 2024-12-29 01:33:15 UTC  
> Updated at: 2025-01-02 16:22:53 UTC  
> Url: https://github.com/boostorg/context/issues/278  

# rational  
  
when do "jump_fcontext", we have an opportunity to pass "void*" to target coroutine. how to do with that argument is up to the target coroutine.  
  
some time we do want to do some 'clean up job' right before return to the target coroutine. because there exist some task that must not be executed on current stack. for example   , free current stack memory.  
  
and for muti-threaded environment, we do need a way to distinguish where a context-switch is finished.  
  
by changing jump_fcontext to `transfer_t jump_fcontext( fcontext_t const to, void * vp, transfer_t (* fn)( fcontext_t from, void*));`   
we can now:  
  
- pass a cleanup function to clean stack right after stack pointer got swapped.  
- notify the scheduler that "from coroutine context" is now safe to be used as "to coroutine".  
- record "from" that does not relay on jumpee coroutine to do the job. that makes code cleaner and easy to understand.  
- doing things before return to target is a bit faster. because the registers are not restored and then saved-again by "the code that called when jump_context returns" with is in fact doing things for previous  coroutine.  
  
# changes to the assembly code  
  
right after loading RSP from "to".  
add this lines  
```  
    test %rdx, %rdx  // test for 3th argument, aka callback_fn  
    je skip_call  
    mov %rax, %rsi  // pass from:fctx argument to callback_fn as first argument, %rdi is already the same argument  
    call * %rdx // call callback_fn, and use its result as  return , do not touch %rax:%rdx pair.  
skip_call:  
    // original code that retore  registers  
```

---

## Comment 1

> Username: olk  
> Created at: 2025-01-01 09:26:40 UTC  
> Url: https://github.com/boostorg/context/issues/278#issuecomment-2566926275  

executing a function before resuming a coroutine is done via ontop_fcontext() - wouldn't this satisfy your needs?

---

## Comment 2

> Username: microcai  
> Created at: 2025-01-02 13:17:15 UTC  
> Url: https://github.com/boostorg/context/issues/278#issuecomment-2567761675  

> executing a function before resuming a coroutine is done via ontop_fcontext() - wouldn't this satisfy your needs?  
  
didn't know ontop_fcontext ;)  
  
it suits my needs !  
  
thanks a lot.

---

## Comment 3

> Username: microcai  
> Created at: 2025-01-02 16:22:52 UTC  
> Url: https://github.com/boostorg/context/issues/278#issuecomment-2568033623  

> executing a function before resuming a coroutine is done via ontop_fcontext() - wouldn't this satisfy your needs?  
  
the API satisfy my needs. but when I look into the implementation, no.   
the "ontop_fcontext" invoke  fn  after it "restored" the context. which is then clobbered and callee saved by fn.  
that hurt performance.
