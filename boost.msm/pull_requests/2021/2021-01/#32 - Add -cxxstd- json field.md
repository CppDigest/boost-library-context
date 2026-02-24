# #32 [skip ci] Add "cxxstd" json field [Merged]

> Username: eldiener  
> Created at: 2021-01-20 20:43:06 UTC  
> Updated at: 2023-08-29 18:04:22 UTC  
> Merged at: 2023-08-29 18:04:22 UTC  
> Closed at: 2023-08-29 18:04:22 UTC  
> Url: https://github.com/boostorg/msm/pull/32  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries in order to specify the minumum C++ standard compilation level. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---
