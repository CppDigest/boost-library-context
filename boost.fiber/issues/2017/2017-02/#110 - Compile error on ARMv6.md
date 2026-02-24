# #110 - Compile error on ARMv6 [Closed]

> Username: vdav  
> Created at: 2017-02-16 14:20:25 UTC  
> Updated at: 2017-04-02 07:43:18 UTC  
> Closed at: 2017-04-02 07:43:18 UTC  
> Url: https://github.com/boostorg/fiber/issues/110  

Some ARM processors do not support "yield" instruction used in include/boost/fiber/detail/cpu_relax.hpp.  
  
Compilation with GCC will generate error messages such as:  
  
/tmp/ccUV8Ii4.s: Assembler messages:  
/tmp/ccUV8Ii4.s:2171: Error: selected processor does not support ARM mode `yield'  
  
This problem will happen on Raspbian (-march=armv6) and Debian armel (-march=armv4t I believe) for example.

---

## Comment 1

> Username: olk  
> Created at: 2017-02-17 07:01:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/110#issuecomment-280570876  

Do you know an equivalent to x86 'pause' mnemonic?

---

## Comment 2

> Username: vdav  
> Created at: 2017-02-17 13:34:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/110#issuecomment-280650432  

There is no multi-core prior to armv6k. So I do not think there are any equivalent since you are not suppose to use spin-locks on those.

---

## Comment 3

> Username: olk  
> Created at: 2017-02-17 16:46:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/110#issuecomment-280702475  

'pause' is used to prevent pipeline stalling - so it does not depend on multi-core support

---

## Comment 4

> Username: olk  
> Created at: 2017-04-02 07:43:18 UTC  
> Url: https://github.com/boostorg/fiber/issues/110#issuecomment-290970511  

fixed in branch develop - on Odroid-X (ARMv7) it is working.  
Could you check the fix on your device please - you should see a warning that the platform doesn't support yield/pause.  
ty
