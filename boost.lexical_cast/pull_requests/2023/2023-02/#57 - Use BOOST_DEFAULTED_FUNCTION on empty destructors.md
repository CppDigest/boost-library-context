# #57 Use BOOST_DEFAULTED_FUNCTION on empty destructors [Closed]

> Username: ecatmur  
> Created at: 2023-02-22 20:00:57 UTC  
> Updated at: 2023-08-12 07:59:47 UTC  
> Closed at: 2023-08-12 07:59:24 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/57  

The compiler-generated copy constructor and copy assignment operator are deprecated since C++11 on classes with user-declared destructors.  
  
This change allows clean compilation with the -Wdeprecated-copy-dtor/-Wdeprecated-copy-with-user-provided-dtor flag.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2023-08-12 07:59:24 UTC  
> Updated_at: 2023-08-12 07:59:47 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/57#issuecomment-1675776245  

Fixed in https://github.com/boostorg/lexical_cast/commit/844a4d1640a94b3884e3895d77e05c98c74000ce  
  
Many thanks for the report and for the hint on fixing!

---
