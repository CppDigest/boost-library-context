# #477 Add "cxxstd" json field [Merged]

> Username: eldiener  
> Created at: 2020-12-31 00:57:44 UTC  
> Updated at: 2021-01-06 16:12:47 UTC  
> Merged at: 2021-01-06 16:12:24 UTC  
> Closed at: 2021-01-06 16:12:24 UTC  
> Url: https://github.com/boostorg/math/pull/477  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: eldiener  
> Created_at: 2021-01-06 13:01:21 UTC  
> Url: https://github.com/boostorg/math/pull/477#issuecomment-755286186  

Would you please merge this PR ?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-01-06 16:12:47 UTC  
> Url: https://github.com/boostorg/math/pull/477#issuecomment-755394883  

Apologies, done.  Thanks for taking this on!

---
