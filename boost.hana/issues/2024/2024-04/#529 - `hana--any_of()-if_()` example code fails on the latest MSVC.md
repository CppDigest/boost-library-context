# #529 - `hana::any_of()/if_()` example code fails on the latest MSVC [Closed]

> Username: Arech  
> Created at: 2024-04-23 20:30:41 UTC  
> Updated at: 2024-05-07 14:34:33 UTC  
> Closed at: 2024-05-07 14:34:33 UTC  
> Url: https://github.com/boostorg/hana/issues/529  

Hey guys and gals, thank you for this awesome library!  
  
I got a weird issue with `hana::any_of()` that I was even able to reproduce on godbolt: https://godbolt.org/z/6eaxaahYj  
  
In short, even example code of `hana::any_of()` as described in the docs: https://www.boost.org/doc/libs/1_84_0/libs/hana/doc/html/group__group-_searchable.html#ga5f7ff0125c448983e1b96c3ffb84f646 fails on the latest MSVC, but compiles nice on (not so latest) gcc & clang.  
  
I've encountered that using boost v1.84 from vcpkg baseline `898b728edc5e0d12b50015f9cd18247c4257a3eb`, but I assume godbolt uses different baseline and maybe even version, and nonetheless it fails there too with exactly the same diagnosis:  
  
```  
boost/hana/eval_if.hpp(60): error C2446: ':': no conversion from 'const boost::hana::integral_constant<bool,false>' to 'const boost::hana::integral_constant<bool,true>'  
boost/hana/eval_if.hpp(60): note: No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
....  
```  
  
I tried [/Zc:__cplusplus](https://learn.microsoft.com/en-us/cpp/build/reference/zc-cplusplus?view=msvc-170) [suggestion](https://github.com/boostorg/hana/issues/516#issuecomment-1803643798), but no luck...  
  
My MSVC Version 17.9.6. `cl.exe` version   
```  
Microsoft (R) C/C++ Optimizing Compiler Version 19.39.33523 for x64  
```  
  
I'll very much appreciate suggestions how to fix that. I didn't expect such issue, honestly...

---

## Comment 1

> Username: Arech  
> Created at: 2024-04-24 08:24:30 UTC  
> Url: https://github.com/boostorg/hana/issues/529#issuecomment-2074376818  

Same for `hana::if_()`:  
- example https://www.boost.org/doc/libs/1_84_0/libs/hana/doc/html/group__group-_logical.html#gafd655d2222367131e7a63616e93dd080  
- result on MSVC  https://godbolt.org/z/cvejWKes6

---

## Comment 2

> Username: Arech  
> Created at: 2024-04-24 08:32:15 UTC  
> Url: https://github.com/boostorg/hana/issues/529#issuecomment-2074392426  

`eval_if()` [example](https://www.boost.org/doc/libs/1_84_0/libs/hana/doc/html/group__group-_logical.html#gab64636f84de983575aac0208f5fa840c) works though: https://godbolt.org/z/rWvdvjPKv

---

## Comment 3

> Username: Arech  
> Created at: 2024-05-07 14:34:33 UTC  
> Url: https://github.com/boostorg/hana/issues/529#issuecomment-2098556371  

I posted this as a potential bug in MSVC bug tracker and just got a reply from their compiler team, that it compiles with [/Zc:ternary](https://learn.microsoft.com/en-us/cpp/build/reference/zc-ternary?view=msvc-170) and indeed, it at least compiles: https://godbolt.org/z/sWeG3s3nv  
  
I guess this concludes the ticket, so I'm closing it.  
~~Hana is so good that I expected it to be more lively supported :D~~
