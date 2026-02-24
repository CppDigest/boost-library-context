# #122 Use BOOST_DEFAULTED_FUNCTION on empty destructors [Merged]

> Username: ecatmur  
> Created at: 2023-02-22 18:50:55 UTC  
> Updated at: 2023-03-06 02:07:10 UTC  
> Merged at: 2023-02-23 11:39:17 UTC  
> Closed at: 2023-02-23 11:39:18 UTC  
> Url: https://github.com/boostorg/program_options/pull/122  

The compiler-generated copy constructor and copy assignment operator are deprecated since C++11 on classes with user-declared destructors.  
  
This change allows clean compilation with the -Wdeprecated-copy-dtor/-Wdeprecated-copy-with-user-provided-dtor flag.

---

## Comment 1

> Username: vprus  
> Created_at: 2023-02-23 11:38:46 UTC  
> Url: https://github.com/boostorg/program_options/pull/122#issuecomment-1441612661  

Thanks for the PR!  
  
While the tests still did not fully complete, the change seems safe enough, and the completed tests are green, so I'll merge it.

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-03-06 02:07:09 UTC  
> Url: https://github.com/boostorg/program_options/pull/122#issuecomment-1455318819  

This change seems to break msvc-12.0.

---
