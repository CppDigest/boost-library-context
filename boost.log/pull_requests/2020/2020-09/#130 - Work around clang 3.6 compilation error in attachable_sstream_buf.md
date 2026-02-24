# #130 Work around clang 3.6 compilation error in attachable_sstream_buf. [Closed]

> Username: Lastique  
> Created at: 2020-09-08 20:51:28 UTC  
> Updated at: 2020-09-09 09:48:51 UTC  
> Closed at: 2020-09-09 09:48:51 UTC  
> Url: https://github.com/boostorg/log/pull/130  

It looks like the compiler has a bug that prevents it from finding  
attachable_sstream_buf::append that takes a pointer as the first  
argument.  
  
This commit tries to disable __builtin_assume for the compiler. The  
builtin was enabled recently and could have triggered the bug.

---
