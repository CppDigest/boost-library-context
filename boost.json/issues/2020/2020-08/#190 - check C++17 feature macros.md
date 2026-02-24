# #190 - check C++17 feature macros [Closed]

> Username: vinniefalco  
> Created at: 2020-08-23 16:54:55 UTC  
> Updated at: 2020-08-24 22:00:38 UTC  
> Closed at: 2020-08-24 22:00:38 UTC  
> Url: https://github.com/boostorg/json/issues/190  

We should check `__cpp_lib_memory_resource` and `__cpp_lib_string_view` to generate a compile error in standalone mode if the features are not present. This solves the problem of defective toolchains which only partially support C++17.
