# #141 Explicitly default copy assignment operator [Open]

> Username: ecatmur  
> Created at: 2023-02-22 20:01:07 UTC  
> Updated at: 2023-02-22 20:01:07 UTC  
> Url: https://github.com/boostorg/range/pull/141  

The compiler-generated copy assignment operator is deprecated since C++11 on classes with user-declared copy constructor.  
  
This change allows clean compilation with the -Wdeprecated-copy/-Wdeprecated-copy-with-user-provided-ctor flag.

---
