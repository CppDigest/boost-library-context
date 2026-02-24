# #37 - NEON registers [Closed]

> Username: mofr  
> Created at: 2016-11-24 15:12:08 UTC  
> Updated at: 2016-11-25 14:54:18 UTC  
> Closed at: 2016-11-25 14:37:03 UTC  
> Url: https://github.com/boostorg/context/issues/37  

Currently, `jump_fcontext` on ARM 32 doesn't either save or restore VFP registers (used by NEON).  
It can lead to serious problems, for example, when compiler uses VFP registers during optimization.  
  
Is it done by intention?

---

## Comment 1

> Username: olk  
> Created at: 2016-11-24 16:44:30 UTC  
> Url: https://github.com/boostorg/context/issues/37#issuecomment-262815359  

It was requested by users not to preserve registers s16-s31.

---

## Comment 2

> Username: mofr  
> Created at: 2016-11-24 16:49:38 UTC  
> Updated at: 2016-11-24 16:51:22 UTC  
> Url: https://github.com/boostorg/context/issues/37#issuecomment-262816244  

Do you remember why it was requested? And how to deal with it in case of compiler optimizations?  
I'm asking because it violates ARMv7 calling conventions and compiler generates "wrong" code in conjunction with boost.context and thread switching.

---

## Comment 3

> Username: olk  
> Created at: 2016-11-24 16:56:44 UTC  
> Url: https://github.com/boostorg/context/issues/37#issuecomment-262817482  

I was told I should remove it because it consumes cycles isn't needed. One year ago boost.context has preserved the FP-env stuff.

---

## Comment 4

> Username: olk  
> Created at: 2016-11-24 18:41:11 UTC  
> Url: https://github.com/boostorg/context/issues/37#issuecomment-262830676  

I would add something like:  
#if (defined(__VFP_FP__) && !defined(__SOFTFP__))  
push {d8-d15}  
#endif  
...  
#if (defined(__VFP_FP__) && !defined(__SOFTFP__))  
pop {d8-d15}  
#endif

---

## Comment 5

> Username: olk  
> Created at: 2016-11-25 12:57:01 UTC  
> Url: https://github.com/boostorg/context/issues/37#issuecomment-262952775  

Could yout test branch fp (https://github.com/boostorg/context/tree/fp) please?  
I've no access to a ARM board at the moment.

---

## Comment 6

> Username: mofr  
> Created at: 2016-11-25 13:22:34 UTC  
> Url: https://github.com/boostorg/context/issues/37#issuecomment-262956452  

Unfortunately, I'm using copy-pasted version of boost. However, i've applied your changes into my version and issue has gone.  
Thanks for your help and fast replies.

---

## Comment 7

> Username: olk  
> Created at: 2016-11-25 14:37:03 UTC  
> Url: https://github.com/boostorg/context/issues/37#issuecomment-262968777  

Thank you too!

---

## Comment 8

> Username: mofr  
> Created at: 2016-11-25 14:54:18 UTC  
> Url: https://github.com/boostorg/context/issues/37#issuecomment-262971910  

Do you plan to enable such option in future releases?
