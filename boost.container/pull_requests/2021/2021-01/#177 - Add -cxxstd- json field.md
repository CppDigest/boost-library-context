# #177 [skip ci] Add "cxxstd" json field [Merged]

> Username: eldiener  
> Created at: 2021-01-19 18:17:35 UTC  
> Updated at: 2021-03-14 07:12:12 UTC  
> Merged at: 2021-03-14 07:12:11 UTC  
> Closed at: 2021-03-14 07:12:11 UTC  
> Url: https://github.com/boostorg/container/pull/177  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries in order to specify the minumum C++ standard compilation level. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: eldiener  
> Created_at: 2021-03-14 03:56:13 UTC  
> Url: https://github.com/boostorg/container/pull/177#issuecomment-798828480  

Is there any reason you can not merge this PR to 'develop' and that there is quite a few 'develop' changes that have never been merged to 'master' for the upcoming 1.76 Boost release ?

---
