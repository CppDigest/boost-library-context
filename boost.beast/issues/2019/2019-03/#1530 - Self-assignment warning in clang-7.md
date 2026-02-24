# #1530 - Self-assignment warning in clang-7 [Closed]

> Username: djarek  
> Created at: 2019-03-20 18:18:37 UTC  
> Updated at: 2019-03-24 03:20:34 UTC  
> Closed at: 2019-03-24 03:20:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1530  

Buffer tests produce the following warning in clang-7:  
```  
clang-linux.compile.c++.without-pch bin.v2/libs/beast/test/beast/core/buffers_suffix.test/clang-linux-7.0.0/debug/cxxstd-11-iso/threadapi-pthread/threading-multi/visibility-hidden/buffers_suffix.o  
In file included from libs/beast/test/beast/core/buffers_suffix.cpp:13:  
libs/beast/test/beast/core/test_buffer.hpp:420:16: warning: explicitly assigning value of variable of type 'DynamicBuffer' to itself [-Wself-assign-overloaded]  
            b2 = b2;  
            ~~ ^ ~~  
1 warning generated.  
```  
  
In this case the warning is benign, because we are actually testing self-assignment.

---

## Comment 1

> Username: djarek  
> Created at: 2019-03-24 03:20:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1530#issuecomment-475924640  

No longer reproduces in develop.
