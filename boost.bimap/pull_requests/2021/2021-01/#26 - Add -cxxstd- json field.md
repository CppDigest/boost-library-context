# #26 [skip ci] Add "cxxstd" json field [Merged]

> Username: eldiener  
> Created at: 2021-01-19 05:06:23 UTC  
> Updated at: 2024-09-22 15:21:11 UTC  
> Merged at: 2024-09-22 15:21:11 UTC  
> Closed at: 2024-09-22 15:21:11 UTC  
> Url: https://github.com/boostorg/bimap/pull/26  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries in order to specify the minumum C++ standard compilation level. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---
