# #43 - need std::thread functionality in __DOS__ and __DJGPP__ [Closed]

> Username: jmichae3  
> Created at: 2017-10-14 18:31:46 UTC  
> Updated at: 2017-10-18 18:46:40 UTC  
> Closed at: 2017-10-18 18:46:40 UTC  
> Url: https://github.com/boostorg/regex/issues/43  

should be functional in #if defined(__DOS__)&&(defined(__WATCOMC__)||defined(__MARSC)||defined(__DJGPP__)) compilers - all they have now is fsu pthreads and pth which I think are timer-based.  
compiling for DOS requires a 32-bit Windows OS currently. maybe not for Mars C/C++.  
please consider this.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-10-15 08:38:49 UTC  
> Url: https://github.com/boostorg/regex/issues/43#issuecomment-336695411  

What has this to do with Boost.Regex?

---

## Comment 2

> Username: jmichae3  
> Created at: 2017-10-17 01:54:14 UTC  
> Url: https://github.com/boostorg/regex/issues/43#issuecomment-337093985  

this was for std::thread sorry.
