# #70 - build issues with avx2 now required [Closed]

> Username: btiplitz  
> Created at: 2019-01-07 21:25:27 UTC  
> Updated at: 2019-01-07 22:58:39 UTC  
> Closed at: 2019-01-07 21:42:53 UTC  
> Url: https://github.com/boostorg/log/issues/70  

The compiler detection when upgrading from 1.65 to 1.68, now sets the flag compiler-supports-avx2 which is then used to build in avx2 instructions into the library within the log library. There appears to be no way to disable this other than modifying the log library (or changing the jam file after the failure).  
  
I only noticed this as I upgraded to gcc-4.9.4 with C++11 support but the binutils does not support avx2 instructions, so I can't build boost 1.68 on the target host as delivered.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-01-07 21:42:53 UTC  
> Url: https://github.com/boostorg/log/issues/70#issuecomment-452092291  

You have to update the compiler and binutils in sync. We assume the compiler supports AVX2, if it is able to generate AVX2 instructions in assembly and translate those into an object file. That implies that binutils are able to translate AVX2 assembly to object file. I'm not sure how your config is able to pass the compiler check and still not able to compile the library, but I can't support a config where the compiler capabilities don't match that of binutils.

---

## Comment 2

> Username: btiplitz  
> Created at: 2019-01-07 21:45:36 UTC  
> Url: https://github.com/boostorg/log/issues/70#issuecomment-452093049  

the compiler was not built with a new assembler so it does not support AVX2.  It appears the build system does not know that, but I've not figured out why.  gcc does not require a newer binutils

---

## Comment 3

> Username: Lastique  
> Created at: 2019-01-07 22:03:02 UTC  
> Url: https://github.com/boostorg/log/issues/70#issuecomment-452098028  

> the compiler was not built with a new assembler so it does not support AVX2.  
  
AFAIK, the compiler will support AVX2 even if binutils don't. The "support" being the ability to translate compiler intrinsics into assembler instructions.  
  
I think I've found the reason why your config could have passed the compiler check. I've committed 3e6fee4 to mitigate this.

---

## Comment 4

> Username: btiplitz  
> Created at: 2019-01-07 22:44:43 UTC  
> Url: https://github.com/boostorg/log/issues/70#issuecomment-452108992  

Thanks -  I see if that mitigates the issue.  It appears the avx2 code won't run if built with avx2, but then run on a system without it.  Is that correct ?

---

## Comment 5

> Username: Lastique  
> Created at: 2019-01-07 22:58:39 UTC  
> Url: https://github.com/boostorg/log/issues/70#issuecomment-452112521  

Yes, AVX2 will only execute on CPUs that support it. On CPUs that don't another code branch will execute.
