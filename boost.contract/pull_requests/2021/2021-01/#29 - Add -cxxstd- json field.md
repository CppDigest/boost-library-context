# #29 [skip ci] Add "cxxstd" json field [Closed]

> Username: eldiener  
> Created at: 2021-01-19 18:18:57 UTC  
> Updated at: 2025-05-31 19:08:07 UTC  
> Closed at: 2025-05-31 19:08:06 UTC  
> Url: https://github.com/boostorg/contract/pull/29  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries in order to specify the minumum C++ standard compilation level. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: jeking3  
> Created_at: 2025-05-31 19:08:06 UTC  
> Url: https://github.com/boostorg/contract/pull/29#issuecomment-2925597800  

This was added ad some point in the past, and is being updated to "11" with some CI fixes.  Closing.

---
