# #429 - Warnings on Windows GCC in header code [Open]

> Username: correaa  
> Created at: 2024-07-23 23:24:17 UTC  
> Updated at: 2024-10-27 07:41:14 UTC  
> Url: https://github.com/boostorg/test/issues/429  

This is similar to the previous issue but on Windows GCC,  
  
https://github.com/correaa/boost-multi/actions/runs/10067263974/job/27830388396?pr=56#step:4:8634  
```  
gcc.compile.c++ bin.v2\libs\boost-multi\test\zero_dimensionality.test\gcc-8\release\x86_64\cxxstd-2a-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\zero_dimensionality.o  
In file included from ./boost/test/included/unit_test.hpp:20,  
                 from libs/boost-multi/test/zero_dimensionality.cpp:25:  
./boost/test/impl/debug.ipp: In instantiation of 'void boost::debug::{anonymous}::dyn_symbol(T&, const char*, const char*) [with T = int (*)()]':  
./boost/test/impl/debug.ipp:167:72:   required from here  
./boost/test/impl/debug.ipp:143:11: error: cast between incompatible function types from 'FARPROC' {aka 'long long int (*)()'} to 'int (*)()' [-Werror=cast-function-type]  
     res = reinterpret_cast<T>( ::GetProcAddress( m, symbol_name ) );  
           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: LuminaScript  
> Created at: 2024-10-26 21:15:08 UTC  
> Url: https://github.com/boostorg/test/issues/429#issuecomment-2439738600  

Hi @correaa I am new to the community and I am interested in this issue. Can you assign it to me? Thanks!

---

## Comment 2

> Username: correaa  
> Created at: 2024-10-26 21:32:32 UTC  
> Updated at: 2024-10-27 07:41:14 UTC  
> Url: https://github.com/boostorg/test/issues/429#issuecomment-2439742480  

Thanks @LuminaScript , but I don't think I have the privileges to assign issues. I am not part of the project.
