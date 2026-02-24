# #381 Use BOOST_DEFAULTED_FUNCTION on empty destructors [Open]

> Username: ecatmur  
> Created at: 2023-02-22 20:01:30 UTC  
> Updated at: 2023-02-22 20:01:30 UTC  
> Url: https://github.com/boostorg/thread/pull/381  

The compiler-generated copy constructor and copy assignment operator are deprecated since C++11 on classes with user-declared destructors.  
  
This change allows clean compilation with the -Wdeprecated-copy-dtor/-Wdeprecated-copy-with-user-provided-dtor flag.

---
