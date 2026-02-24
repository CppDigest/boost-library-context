# #138 Add support for TCC [Merged]

> Username: sleeptightAnsiC  
> Created at: 2025-01-14 03:38:42 UTC  
> Updated at: 2025-02-03 18:57:01 UTC  
> Merged at: 2025-02-03 12:29:44 UTC  
> Closed at: 2025-02-03 12:29:44 UTC  
> Url: https://github.com/boostorg/predef/pull/138  

Hi,  
  
I've tested boost.predef against TCC and it works just fine besides detecting the compiler itself.  
This PR adds support for it by checking `__TINYC__` macro and setting the corresponding version.  
TCC is not a C++ compiler, but since boost.predef supports C, I thought it would be nice to add.  
  
Tiny C Compiler (or TinyCC or TCC for short) is a minimal implementation of GCC-like compiler, aiming for fast compile times. It supports AnsiC, C99 and partially C11 with some GNUC extensions. Development is a bit staled (with latest release being in 2017) but it is still alive and some FOSS projects support it.  
  
References:  
https://repo.or.cz/w/tinycc.git  
https://en.wikipedia.org/wiki/Tiny_C_Compiler

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2025-01-17 13:08:00 UTC  
> Url: https://github.com/boostorg/predef/pull/138#issuecomment-2598332631  

Thank you for the contribution. I'm looking into adding TCC testing to verify works as expected.

---

## Comment 2

> Username: sleeptightAnsiC  
> Created_at: 2025-01-17 22:11:20 UTC  
> Updated_at: 2025-02-03 18:57:01 UTC  
> Url: https://github.com/boostorg/predef/pull/138#issuecomment-2599296700  

Thanks for taking a look at this @grafikrobot ,  
  
If you're are looking for some more info, references and rationale for this change, here you go (I probably should mention these in my first message):  
  
<details><summary>[links and references]</summary>  
<p>  
  
* github mirror of TCC: https://github.com/TinyCC/tinycc  
* you can find conditional predefs that TCC is defining, here: https://github.com/TinyCC/tinycc/blob/mob/include/tccdefs.h  
* another place where TCC defines stuff, this time at runtime:  
https://github.com/TinyCC/tinycc/blob/f6385c05308f715bdd2c06336801193a21d69b50/tccpp.c#L3595-L3642  
* I found multiple places where TCC tries to emulate GCC, that's why my PR adds the same include for TCC in `include/boost/predef/compiler/gcc.h` as Clang has, here's example:  
https://github.com/TinyCC/tinycc/blob/f6385c05308f715bdd2c06336801193a21d69b50/include/tccdefs.h#L85-L89  
* TCC 0.9.27 should still be available in multiple software distros (I presume you guys are using distro package managers for testing so you may need one of these):  
** scoop https://scoop.sh/#/apps?q=tcc&id=a77e04588055340b4049389dcfa0107d642a7e3a  
** debian https://tracker.debian.org/pkg/tcc  
** homebrew https://formulae.brew.sh/formula/tcc  
** arch https://archlinux.org/packages/extra/x86_64/tcc/  
* compiler explorer link: https://godbolt.org/z/We4s38bEM  
* (I can also check for predefs on other environments, have few VMs configured for other OSes and CPU architectures, just didn't want to spam it here, but let me know if you need them checked)  
  
</p>  
</details>   
  
<details><summary>[predefs that TCC 0.9.28 (latest git trunk) generates on my local x86_64 Linux machine]</summary>  
<p>  
  
```  
$ uname -a ; tcc --version ; echo | tcc -dM -E -  
Linux MAL200424 6.12.8-arch1-1 #1 SMP PREEMPT_DYNAMIC Thu, 02 Jan 2025 22:52:26 +0000 x86_64 GNU/Linux  
tcc version 0.9.28rc 2024-10-27 mob@a21b5f1f (x86_64 Linux)  
#define __TINYC__ 928  
#define __x86_64__ 1  
#define __amd64__ 1  
#define __linux__ 1  
#define __linux 1  
#define __unix__ 1  
#define __unix 1  
#define __TCC_PP__ 1  
#define __SIZEOF_POINTER__ 8  
#define __SIZEOF_LONG__ 8  
#define __STDC__ 1  
#define __STDC_VERSION__ 199901L  
#define __SIZE_TYPE__ unsigned long  
#define __PTRDIFF_TYPE__ long  
#define __LP64__ 1  
#define __INT64_TYPE__ long  
#define __SIZEOF_INT__ 4  
#define __INT_MAX__ 0x7fffffff  
#define __LONG_MAX__ 0x7fffffffffffffffL  
#define __SIZEOF_LONG_LONG__ 8  
#define __LONG_LONG_MAX__ 0x7fffffffffffffffLL  
#define __CHAR_BIT__ 8  
#define __ORDER_LITTLE_ENDIAN__ 1234  
#define __ORDER_BIG_ENDIAN__ 4321  
#define __BYTE_ORDER__ __ORDER_LITTLE_ENDIAN__  
#define __WCHAR_TYPE__ int  
#define __WINT_TYPE__ unsigned int  
#define __UINTPTR_TYPE__ unsigned __PTRDIFF_TYPE__  
#define __INTPTR_TYPE__ __PTRDIFF_TYPE__  
#define __INT32_TYPE__ int  
#define __REDIRECT(name,proto,alias) name proto __asm__(#alias)  
#define __REDIRECT_NTH(name,proto,alias) name proto __asm__(#alias)__THROW  
#define __REDIRECT_NTHNL(name,proto,alias) name proto __asm__(#alias)__THROWNL  
#define __PRETTY_FUNCTION__ __FUNCTION__  
#define __has_builtin(x) 0  
#define __has_feature(x) 0  
#define __has_attribute(x) 0  
#define _Nonnull  
#define _Nullable  
#define _Nullable_result  
#define _Null_unspecified  
#define __BASE_FILE__ "-"  
```  
  
</p>  
</details>   
  
I hope this will help you.

---
