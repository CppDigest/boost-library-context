# #39 - Unknown directive .cpu on aarch64 with Clang 3.8 on Android [Closed]

> Username: Orphis  
> Created at: 2017-01-04 02:06:30 UTC  
> Updated at: 2017-01-05 18:19:22 UTC  
> Closed at: 2017-01-05 18:19:22 UTC  
> Url: https://github.com/boostorg/context/issues/39  

The aarch64 files don't compile on Android with the Clang compiler because of the .cpu directive that is unsupported in version 3.8.  
Support has been added to 3.9 [r265240](http://llvm.org/viewvc/llvm-project?view=revision&revision=265240)  but that version of the compiler hasn't been released with any NDK yet.  
  
Could the offending lines be removed from all 3 arm64 elf files?  
Note there's no regression test runs for aarch64 + clang in the test suite, that would explain why no one noticed before.

---

## Comment 1

> Username: olk  
> Created at: 2017-01-04 05:34:53 UTC  
> Url: https://github.com/boostorg/context/issues/39#issuecomment-270298145  

> Could the offending lines be removed from all 3 arm64 elf files?  
  
could you test and report the result please

---

## Comment 2

> Username: Orphis  
> Created at: 2017-01-04 10:07:13 UTC  
> Url: https://github.com/boostorg/context/issues/39#issuecomment-270336378  

I mean, it works fine on Android if I remove them, with both GCC and Clang.   
I'm just wondering if it is required by some other toolchain for example.

---

## Comment 3

> Username: olk  
> Created at: 2017-01-05 18:19:22 UTC  
> Url: https://github.com/boostorg/context/issues/39#issuecomment-270716503  

done
