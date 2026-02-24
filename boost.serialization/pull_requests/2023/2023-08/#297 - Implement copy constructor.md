# #297 Implement copy constructor [Closed]

> Username: ecatmur  
> Created at: 2023-08-29 01:21:30 UTC  
> Updated at: 2023-08-29 22:50:09 UTC  
> Closed at: 2023-08-29 22:49:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/297  

#280 take 2 (test matrix: toolset=clang-14,gcc-11 variant=debug,release cxxstd=11,14,17,20,2b)  
  
The compiler-generated copy constructor is deprecated since C++11 on classes with user-declared copy assignment operator.  
  
This change allows clean compilation with the -Wdeprecated-copy/-Wdeprecated-copy-with-user-provided-ctor flag.

---

## Comment 1

> Username: robertramey  
> Created_at: 2023-08-29 22:50:08 UTC  
> Url: https://github.com/boostorg/serialization/pull/297#issuecomment-1698244372  

merged and tested

---
