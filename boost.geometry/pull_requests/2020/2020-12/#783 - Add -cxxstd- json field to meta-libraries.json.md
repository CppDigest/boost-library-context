# #783 Add "cxxstd" json field to meta/libraries.json [Merged]

> Username: eldiener  
> Created at: 2020-12-16 07:06:04 UTC  
> Updated at: 2020-12-20 10:15:30 UTC  
> Merged at: 2020-12-20 10:15:30 UTC  
> Closed at: 2020-12-20 10:15:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/783  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-12-16 14:03:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/783#issuecomment-746334993  

Here is the discussion for those who need to check the background of this PR https://lists.boost.org/Archives/boost/2020/12/250543.php

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-12-19 23:42:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/783#issuecomment-748540365  

Can someone please merge this PR ?

---
