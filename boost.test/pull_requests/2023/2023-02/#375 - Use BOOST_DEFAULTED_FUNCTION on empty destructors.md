# #375 Use BOOST_DEFAULTED_FUNCTION on empty destructors [Merged]

> Username: ecatmur  
> Created at: 2023-02-22 20:01:25 UTC  
> Updated at: 2023-06-07 06:59:56 UTC  
> Merged at: 2023-06-07 06:59:56 UTC  
> Closed at: 2023-06-07 06:59:56 UTC  
> Url: https://github.com/boostorg/test/pull/375  

The compiler-generated copy constructor and copy assignment operator are deprecated since C++11 on classes with user-declared destructors.  
  
This change allows clean compilation with the -Wdeprecated-copy-dtor/-Wdeprecated-copy-with-user-provided-dtor flag.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-02-22 20:44:01 UTC  
> Url: https://github.com/boostorg/test/pull/375#issuecomment-1440772125  

This looks correct to me. I'll check again once the CI system cycles.

---
