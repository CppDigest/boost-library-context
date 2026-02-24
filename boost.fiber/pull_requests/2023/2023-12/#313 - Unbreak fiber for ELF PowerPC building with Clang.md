# #313 Unbreak fiber for ELF PowerPC building with Clang [Merged]

> Username: brad0  
> Created at: 2023-12-23 23:16:22 UTC  
> Updated at: 2023-12-24 07:39:40 UTC  
> Merged at: 2023-12-24 07:11:17 UTC  
> Closed at: 2023-12-24 07:11:17 UTC  
> Url: https://github.com/boostorg/fiber/pull/313  

Clang also defines the uppercase ```__POWERPC__``` symbol so the commit  
2bd8eb02f81ab8297aa89b8cb29b0f3867b9fafe breaks the build on ELF  
PowerPC systems. Since we know this is already a PowerPC system  
with ```BOOST_ARCH_PPC``` then check for the vendor ```__APPLE__``` instead.

---

## Comment 1

> Username: brad0  
> Created_at: 2023-12-23 23:49:15 UTC  
> Url: https://github.com/boostorg/fiber/pull/313#issuecomment-1868391290  

cc @barracuda156

---

## Comment 2

> Username: barracuda156  
> Created_at: 2023-12-24 04:44:27 UTC  
> Url: https://github.com/boostorg/fiber/pull/313#issuecomment-1868431860  

@brad0 Thank you, this is something new to hear. Which OS and which clang? Just so that I know.

---

## Comment 3

> Username: brad0  
> Created_at: 2023-12-24 04:46:17 UTC  
> Url: https://github.com/boostorg/fiber/pull/313#issuecomment-1868432018  

> @brad0 Thank you, this is something new to hear. Which OS and which clang? Just so that I know.  
  
OpenBSD, but this would affect any ELF OS. e.g. *BSD, Linux if building with Clang.

---

## Comment 4

> Username: barracuda156  
> Created_at: 2023-12-24 04:50:34 UTC  
> Url: https://github.com/boostorg/fiber/pull/313#issuecomment-1868432456  

Good to know. Should be careful then to always check for `__APPLE__` && `__POWERPC__`. Or at least exclude Clang (`__POWERPC__` is also defined on BeOS, AFAIR, but this is not relevant to the present case).

---

## Comment 5

> Username: brad0  
> Created_at: 2023-12-24 05:06:19 UTC  
> Url: https://github.com/boostorg/fiber/pull/313#issuecomment-1868434075  

> Good to know. Should be careful then to always check for `__APPLE__` && `__POWERPC__`. Or at least exclude Clang (`__POWERPC__` is also defined on BeOS, AFAIR, but this is not relevant to the present case).  
  
symbols defined for Clang PowerPC targets..  
  
```  
  // Target identification.  
  Builder.defineMacro("__ppc__");  
  Builder.defineMacro("__PPC__");  
  Builder.defineMacro("_ARCH_PPC");  
  Builder.defineMacro("__powerpc__");  
  Builder.defineMacro("__POWERPC__");  
  if (PointerWidth == 64) {  
    Builder.defineMacro("_ARCH_PPC64");  
    Builder.defineMacro("__powerpc64__");  
    Builder.defineMacro("__PPC64__");  
  }  
```

---

## Comment 6

> Username: barracuda156  
> Created_at: 2023-12-24 05:11:13 UTC  
> Url: https://github.com/boostorg/fiber/pull/313#issuecomment-1868434607  

> > Good to know. Should be careful then to always check for `__APPLE__` && `__POWERPC__`. Or at least exclude Clang (`__POWERPC__` is also defined on BeOS, AFAIR, but this is not relevant to the present case).  
>   
>   
>   
> symbols defined for Clang PowerPC targets..  
>   
>   
>   
> ```  
>   
>   // Target identification.  
>   
>   Builder.defineMacro("__ppc__");  
>   
>   Builder.defineMacro("__PPC__");  
>   
>   Builder.defineMacro("_ARCH_PPC");  
>   
>   Builder.defineMacro("__powerpc__");  
>   
>   Builder.defineMacro("__POWERPC__");  
>   
>   if (PointerWidth == 64) {  
>   
>     Builder.defineMacro("_ARCH_PPC64");  
>   
>     Builder.defineMacro("__powerpc64__");  
>   
>     Builder.defineMacro("__PPC64__");  
>   
>   }  
>   
> ```  
  
(On a side note, what a mess: why would it define `__ppc__` for 32- and 64-bit both?)  
  
For BeOS, it does use `__POWERPC__`, but I am not sure about its assembler syntax. It may be using unprefixed registers in fact, judging from https://opensource.apple.com/source/gcc3/gcc3-1175/gcc/config/rs6000/beos.h.auto.html  
If so, your change may fix BeOS too.

---

## Comment 7

> Username: brad0  
> Created_at: 2023-12-24 05:16:00 UTC  
> Url: https://github.com/boostorg/fiber/pull/313#issuecomment-1868435102  

> (On a side note, what a mess: why would it define `__ppc__` for 32- and 64-bit both?)  
  
Same goes for ```__powerpc__``` for both.  
  
> For BeOS, it does use `__POWERPC__`, but I am not sure about its assembler syntax. It may be using unprefixed registers in fact, judging from https://opensource.apple.com/source/gcc3/gcc3-1175/gcc/config/rs6000/beos.h.auto.html If so, your change may fix BeOS too.  
  
Even if it had to go the other way you could check add a check for BeOS.

---

## Comment 8

> Username: olk  
> Created_at: 2023-12-24 07:11:21 UTC  
> Url: https://github.com/boostorg/fiber/pull/313#issuecomment-1868449666  

ty

---
