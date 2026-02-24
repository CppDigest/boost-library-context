# #182 - DOS support please (used in embedded, disk utilities) [Closed]

> Username: jmichae3  
> Created at: 2017-10-17 01:56:08 UTC  
> Updated at: 2017-10-17 05:40:48 UTC  
> Closed at: 2017-10-17 05:38:14 UTC  
> Url: https://github.com/boostorg/thread/issues/182  

should be functional in #if defined(DOS)&&(defined(WATCOMC)||defined(__MARSC)||defined(DJGPP)) compilers - all they have now is fsu pthreads and pth which I think are timer-based.  
compiling for DOS requires a 32-bit Windows OS currently. maybe not for Mars C/C++.  
please consider this.

---

## Comment 1

> Username: jmichae3  
> Created at: 2017-10-17 01:56:25 UTC  
> Url: https://github.com/boostorg/thread/issues/182#issuecomment-337094311  

DJGPP is a 32-bit compiler.

---

## Comment 2

> Username: viboes  
> Created at: 2017-10-17 05:38:14 UTC  
> Url: https://github.com/boostorg/thread/issues/182#issuecomment-337123193  

Why are you creating those issues in this repo?
