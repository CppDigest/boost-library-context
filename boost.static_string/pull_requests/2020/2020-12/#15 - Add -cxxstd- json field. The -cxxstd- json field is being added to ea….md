# #15 Add "cxxstd" json field. The "cxxstd" json field is being added to ea… [Closed]

> Username: eldiener  
> Created at: 2020-12-16 06:53:48 UTC  
> Updated at: 2020-12-17 18:23:20 UTC  
> Closed at: 2020-12-17 18:23:20 UTC  
> Url: https://github.com/boostorg/static_string/pull/15  

…ch Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: sdkrystian  
> Created_at: 2020-12-16 07:16:49 UTC  
> Url: https://github.com/boostorg/static_string/pull/15#issuecomment-745816371  

Looks good, I'll merge this tommorow

---

## Comment 2

> Username: sdkrystian  
> Created_at: 2020-12-17 18:23:20 UTC  
> Url: https://github.com/boostorg/static_string/pull/15#issuecomment-747614826  

Merged in 59c4c556bbe6042be3591f0f298573b0734075c1, thank you :)

---
