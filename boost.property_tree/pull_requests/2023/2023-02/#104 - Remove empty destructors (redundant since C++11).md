# #104 Remove empty destructors (redundant since C++11) [Merged]

> Username: ecatmur  
> Created at: 2023-02-22 20:01:02 UTC  
> Updated at: 2023-11-21 22:54:40 UTC  
> Merged at: 2023-11-21 22:54:39 UTC  
> Closed at: 2023-11-21 22:54:39 UTC  
> Url: https://github.com/boostorg/property_tree/pull/104  

The compiler-generated copy constructor and copy assignment operator are deprecated since C++11 on classes with user-declared destructors.  
  
The compiler-generated destructor is `noexcept` since C++11, so there is no need to declare/define a user-provided or explicitly defaulted destructor.  
  
This change allows clean compilation with the -Wdeprecated-copy-dtor/-Wdeprecated-copy-with-user-provided-dtor flag.

---

## Comment 1

> Username: ashtum  
> Created_at: 2023-11-21 10:54:09 UTC  
> Updated_at: 2023-11-21 11:10:09 UTC  
> Url: https://github.com/boostorg/property_tree/pull/104#issuecomment-1820684470  

@ecatmur I believe we don't need BOOST_DEFAULTED_FUNCTION macro anymore, since we have dropped C++03 support.  
Could you please update this PR accordingly so that we can merge it.

---

## Comment 2

> Username: ashtum  
> Created_at: 2023-11-21 16:31:49 UTC  
> Url: https://github.com/boostorg/property_tree/pull/104#issuecomment-1821263529  

@pdimov Could you please approve the workflow?

---
