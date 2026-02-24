# #304 Add "cxxstd" json field. The "cxxstd" json field is being added to ea… [Merged]

> Username: eldiener  
> Created at: 2020-12-16 07:04:50 UTC  
> Updated at: 2020-12-16 17:43:41 UTC  
> Merged at: 2020-12-16 17:41:36 UTC  
> Closed at: 2020-12-16 17:41:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/304  

…ch Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2020-12-16 17:41:39 UTC  
> Url: https://github.com/boostorg/histogram/pull/304#issuecomment-746705833  

Thanks @eldiener !

---

## Comment 2

> Username: henryiii  
> Created_at: 2020-12-16 17:43:41 UTC  
> Url: https://github.com/boostorg/histogram/pull/304#issuecomment-746709202  

PS: This is the first I've heard of this new feature of the Boost website, and it sounds _really_ useful! Looking forward to seeing it roll out.

---
