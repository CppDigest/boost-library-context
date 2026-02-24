# #52 - Failing to build callcc test with g++-4.9 [Closed]

> Username: DaoWen  
> Created at: 2017-04-13 00:30:49 UTC  
> Updated at: 2017-04-13 15:42:54 UTC  
> Closed at: 2017-04-13 15:42:53 UTC  
> Url: https://github.com/boostorg/context/issues/52  

The `callcc` test fails to build when using version 4.9 of the GCC toolchain. The compiler gives an error about ambiguous overloads of the `callcc` function, which I think is [a known bug in gcc-4.9](http://stackoverflow.com/questions/31563235/) that was fixed in gcc-5.  
  
I got the same error on Linux for in both PowerPC (32-bit) and x86_64 builds. Unfortunately, gcc-4.9 was the newest compiler I was able to get installed on the 32-bit PowerPC system for testing, so without support for that toolchain I can't test the ppc/sysv/elf platform (see #50).  
  
I tried adding an extra `disable_overload` to disambiguate the call, which allowed me to compile, but then the test failed due to bad values being passed through the continuation object... I will try to look at this again later, but you're obviously more familiar with the codebase, and I'm hoping if you can get g++-4.9 installed that you'll have a much easier time debugging this.

---

## Comment 1

> Username: olk  
> Created at: 2017-04-13 05:46:04 UTC  
> Updated at: 2017-04-13 05:46:26 UTC  
> Url: https://github.com/boostorg/context/issues/52#issuecomment-293787851  

You could compile the unit-tests without "-std=c++<xyz>" - then only fcontext-API (asm implementation) is tested.  
  
I can check if a workaround is available for gcc-4.8/gcc-4.9 - several versions of clang, intel, msvc are able to build boost.context.

---

## Comment 2

> Username: DaoWen  
> Created at: 2017-04-13 15:35:55 UTC  
> Url: https://github.com/boostorg/context/issues/52#issuecomment-293931309  

> You could compile the unit-tests without "-std=c++" - then only fcontext-API (asm implementation) is tested.  
  
Yes, I actually tried building with gcc-4.7 first (which doesn't support `thread_local`), and the fcontext tests passed. I suppose that should be sufficient for verifying the ppc32 code, so I won't worry about this issue anymore. Thanks!

---

## Comment 3

> Username: olk  
> Created at: 2017-04-13 15:42:53 UTC  
> Url: https://github.com/boostorg/context/issues/52#issuecomment-293933314  

fine - ty
