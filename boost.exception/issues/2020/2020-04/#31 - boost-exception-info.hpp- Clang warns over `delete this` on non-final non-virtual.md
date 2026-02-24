# #31 - boost/exception/info.hpp: Clang warns over `delete this` on non-final non-virtual [Closed]

> Username: Kojoley  
> Created at: 2020-04-04 00:24:35 UTC  
> Updated at: 2020-04-08 00:34:38 UTC  
> Closed at: 2020-04-08 00:34:38 UTC  
> Url: https://github.com/boostorg/exception/issues/31  

```  
In file included from libs\spirit\classic\test\bug_000008.cpp:51:  
In file included from .\boost/thread.hpp:24:  
In file included from .\boost/thread/future.hpp:34:  
In file included from .\boost/thread/exceptional_ptr.hpp:10:  
In file included from .\boost/exception_ptr.hpp:9:  
In file included from .\boost/exception/detail/exception_ptr.hpp:15:  
.\boost/exception/info.hpp(134,21): error: delete called on non-final 'boost::exception_detail::error_info_container_impl' that has virtual functions but non-virtual destructor [-Werror,-Wdelete-non-abstract-non-virtual-dtor]  
                    delete this;  
                    ^  
1 warning and 1 error generated.  
     "clang-cl.exe" -m64 @"bin.v2\libs\spirit\classic\test\bug_000008.test\clang-win-9.0.1\release\address-model-64\threadapi-win32\threading-multi\bug_000008.obj.rsp"   
```  
  
Reproducible with `b2 toolset=clang-win warnings=on warnings-as-errors=on libs/exception/test`

---

## Comment 1

> Username: zajo  
> Created at: 2020-04-08 00:34:38 UTC  
> Url: https://github.com/boostorg/exception/issues/31#issuecomment-610687218  

Should be fixed now. Thanks!
