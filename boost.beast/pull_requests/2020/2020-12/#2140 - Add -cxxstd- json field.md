# #2140 Add "cxxstd" json field.  [Closed]

> Username: eldiener  
> Created at: 2020-12-16 07:02:54 UTC  
> Updated at: 2020-12-17 08:09:50 UTC  
> Closed at: 2020-12-17 08:09:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2140  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2020-12-16 17:33:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2140#issuecomment-746692813  

I have scheduled this into the version 307 PR. Thanks very much for preparing it.

---
