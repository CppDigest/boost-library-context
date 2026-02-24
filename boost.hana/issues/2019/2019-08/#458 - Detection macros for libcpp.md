# #458 - Detection macros for libcpp [Closed]

> Username: Kurkin  
> Created at: 2019-08-09 09:23:04 UTC  
> Updated at: 2020-05-19 03:03:07 UTC  
> Closed at: 2020-05-19 03:03:07 UTC  
> Url: https://github.com/boostorg/hana/issues/458  

False-positive warning generated for _LIBCPP_VERSION == 10000  
```  
boost-1_68_0/boost/hana/config.hpp:99:9: warning: "Versions of libc++ prior to the one shipped with Clang 3.5.0 are not supported by Hana." [-W#warnings]  
#       warning "Versions of libc++ prior to the one shipped with Clang 3.5.0 are not supported by Hana."  
        ^  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2019-09-24 12:54:23 UTC  
> Url: https://github.com/boostorg/hana/issues/458#issuecomment-534544832  

I believe this should have been fixed by https://github.com/boostorg/hana/commit/8f935a710b5c30544da5c89f65bed6f0b7b7e8cf. Can you please confirm whether you're still able to reproduce the problem with Boost 1.71.0?

---

## Comment 2

> Username: ricejasonf  
> Created at: 2020-05-19 03:03:07 UTC  
> Url: https://github.com/boostorg/hana/issues/458#issuecomment-630550722  

I just ran into this. The fix shows up in 1.72.  
Confirmed here: https://godbolt.org/z/9QCn-d  
  
Thank you for the report and the fix.  
I'll go ahead and close this.
