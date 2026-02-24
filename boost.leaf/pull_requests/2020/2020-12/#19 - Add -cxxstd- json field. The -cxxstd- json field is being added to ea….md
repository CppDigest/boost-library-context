# #19 Add "cxxstd" json field. The "cxxstd" json field is being added to ea… [Merged]

> Username: eldiener  
> Created at: 2020-12-16 07:03:38 UTC  
> Updated at: 2020-12-16 18:28:08 UTC  
> Merged at: 2020-12-16 18:27:59 UTC  
> Closed at: 2020-12-16 18:27:59 UTC  
> Url: https://github.com/boostorg/leaf/pull/19  

…ch Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: zajo  
> Created_at: 2020-12-16 18:28:08 UTC  
> Url: https://github.com/boostorg/leaf/pull/19#issuecomment-746781096  

Thank you!

---
