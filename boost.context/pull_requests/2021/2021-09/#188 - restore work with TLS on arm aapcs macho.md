# #188 restore work with TLS[__PTK_LIBC_DYLD_Unwind_SjLj_Key] on arm aapcs macho [Merged]

> Username: apolukhin  
> Created at: 2021-09-24 09:03:43 UTC  
> Updated at: 2021-10-07 15:44:29 UTC  
> Merged at: 2021-10-02 15:59:48 UTC  
> Closed at: 2021-10-02 15:59:48 UTC  
> Url: https://github.com/boostorg/context/pull/188  

I've found this fix in our codebase. Not sure how it works, but my guess is following:  
  
`__PTK_LIBC_DYLD_Unwind_SjLj_Key` is defined in https://opensource.apple.com/source/libpthread/libpthread-137.1.1/private/tsd_private.h as `#define __PTK_LIBC_DYLD_Unwind_SjLj_Key	18`. Multiplying it on a size of a pointer gives `72`.  
  
P.S.: it already was `72` before https://github.com/boostorg/context/commit/763f28542d3c2931ead6eef1409b113155e9a1c9

---

## Comment 1

> Username: olk  
> Created_at: 2021-09-24 09:30:32 UTC  
> Url: https://github.com/boostorg/context/pull/188#issuecomment-926485335  

So MacOS uses setjump/long jump for exception handling implementation?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2021-09-24 09:38:58 UTC  
> Url: https://github.com/boostorg/context/pull/188#issuecomment-926490424  

> So MacOS uses setjump/long jump for exception handling implementation?  
  
Not sure. Just showing a patch and my guesses why it does a proper change

---

## Comment 3

> Username: olk  
> Created_at: 2021-10-02 16:00:11 UTC  
> Url: https://github.com/boostorg/context/pull/188#issuecomment-932775139  

ty - could you check branch develop, please

---

## Comment 4

> Username: apolukhin  
> Created_at: 2021-10-07 15:44:29 UTC  
> Url: https://github.com/boostorg/context/pull/188#issuecomment-937917684  

Testing of the develop branch can not happen soon due to specifics of our development.  
  
However, there's now no diff between develop branch of context and the version that we use. Seems good!  
  
Many thanks!

---
