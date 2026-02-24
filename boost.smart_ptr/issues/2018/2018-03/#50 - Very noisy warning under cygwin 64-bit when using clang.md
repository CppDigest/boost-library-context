# #50 - Very noisy warning under cygwin 64-bit when using clang [Closed]

> Username: jeking3  
> Created at: 2018-03-07 14:15:35 UTC  
> Updated at: 2018-03-08 16:15:18 UTC  
> Closed at: 2018-03-08 16:15:18 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/50  

To reproduce, use a cygwin64 environment and run b2 toolset=clang, and you get this warning appearing on almost every compilation unit:  
```  
clang-linux.compile.c++.without-pth ../../bin.v2/libs/program_options/example/clang-linux-5.0.1/debug/link-static/regex.obj  
In file included from ../../libs/program_options/example/regex.cpp:22:  
In file included from ../../boost/program_options.hpp:15:  
In file included from ../../boost/program_options/options_description.hpp:16:  
In file included from ../../boost/shared_ptr.hpp:17:  
In file included from ../../boost/smart_ptr/shared_ptr.hpp:36:  
In file included from ../../boost/smart_ptr/detail/spinlock_pool.hpp:25:  
In file included from ../../boost/smart_ptr/detail/spinlock.hpp:53:  
In file included from ../../boost/smart_ptr/detail/spinlock_sync.hpp:18:  
../../boost/smart_ptr/detail/yield_k.hpp:64:19: warning: calling convention '__stdcall__' ignored for this target [-Wignored-attributes]  
  extern "C" void __stdcall Sleep( unsigned long ms );  
                  ^  
<built-in>:335:34: note: expanded from here  
#define __stdcall __attribute__((__stdcall__))  
                                 ^  
1 warning generated.  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2018-03-07 17:50:16 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/50#issuecomment-371223432  

They just _had_ to warn.  
  
I'm trying to set up an Appveyor for this configuration, but things don't go well. I suspect that the Cygwin clang is not present on Appveyor and `toolset=clang` is picking up another clang from somewhere.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-03-08 16:15:18 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/50#issuecomment-371536610  

https://github.com/boostorg/smart_ptr/commit/67fab5c9ecc0190044b822140128c10de398ca76
