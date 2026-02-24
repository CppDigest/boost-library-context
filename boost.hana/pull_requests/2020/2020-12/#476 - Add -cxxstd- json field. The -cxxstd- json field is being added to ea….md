# #476 Add "cxxstd" json field. The "cxxstd" json field is being added to ea… [Merged]

> Username: eldiener  
> Created at: 2020-12-16 07:05:25 UTC  
> Updated at: 2020-12-28 02:05:45 UTC  
> Merged at: 2020-12-28 02:05:45 UTC  
> Closed at: 2020-12-28 02:05:45 UTC  
> Url: https://github.com/boostorg/hana/pull/476  

…ch Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-12-27 23:42:40 UTC  
> Url: https://github.com/boostorg/hana/pull/476#issuecomment-751529816  

Checks are failing that have nothing to do with this PR. Would you please merge this PR ?

---
