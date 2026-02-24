# #191 Default empty destructor. [Merged]

> Username: ecatmur  
> Created at: 2023-02-22 20:01:12 UTC  
> Updated at: 2024-03-24 16:47:50 UTC  
> Merged at: 2024-03-24 16:47:49 UTC  
> Closed at: 2024-03-24 16:47:49 UTC  
> Url: https://github.com/boostorg/regex/pull/191  

The compiler-generated copy constructor and copy assignment operator are deprecated since C++11 on classes with user-declared destructors.  
  
This change allows clean compilation with the -Wdeprecated-copy-dtor/-Wdeprecated-copy-with-user-provided-dtor flag.

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2023-08-15 23:02:58 UTC  
> Url: https://github.com/boostorg/regex/pull/191#issuecomment-1679738490  

@jzmaddock Ping

---
