# #407 Default empty destructors [Open]

> Username: ecatmur  
> Created at: 2023-02-22 20:00:45 UTC  
> Updated at: 2023-12-21 21:47:54 UTC  
> Url: https://github.com/boostorg/asio/pull/407  

The compiler-generated copy constructor and copy assignment operator are deprecated since C++11 on classes with user-declared destructors.  
  
This change allows clean compilation with the -Wdeprecated-copy-dtor/-Wdeprecated-copy-with-user-provided-dtor flag.  
  
The source code in this repository is generated from an upstream repository at https://github.com/chriskohlhoff/asio.  
  
Please consider raising new pull requests at https://github.com/chriskohlhoff/asio/pulls.

---
