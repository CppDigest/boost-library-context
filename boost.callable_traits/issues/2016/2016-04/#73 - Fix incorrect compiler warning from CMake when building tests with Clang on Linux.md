# #73 - Fix incorrect compiler warning from CMake when building tests with Clang on Linux [Closed]

> Username: badair  
> Created at: 2016-04-19 01:49:24 UTC  
> Updated at: 2016-06-30 00:51:09 UTC  
> Closed at: 2016-06-30 00:51:09 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/73  



---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-06-23 19:17:45 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/73#issuecomment-228155602  

Perhaps my version of clang is more recent, but I did not see any warnings with `make check` using the following compiler with libstdc++ 5.1.  
  
```  
clang version 3.9.0 (http://llvm.org/git/clang.git 8992cdfa97a0ea84e754f778d1392d390058d8c7) (http://llvm.org/git/llvm.git 45d1685851f646537653dc92a2d946f105867284)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /usr/local/bin  
```

---

## Comment 2

> Username: badair  
> Created at: 2016-06-30 00:51:09 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/73#issuecomment-229531326  

Ok, thanks. I must have fixed this some time ago.
