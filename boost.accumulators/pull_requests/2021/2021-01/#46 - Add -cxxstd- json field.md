# #46 [skip ci] Add "cxxstd" json field [Merged]

> Username: eldiener  
> Created at: 2021-01-19 02:23:45 UTC  
> Updated at: 2022-04-19 08:27:34 UTC  
> Merged at: 2022-04-19 08:27:34 UTC  
> Closed at: 2022-04-19 08:27:34 UTC  
> Url: https://github.com/boostorg/accumulators/pull/46  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries in order to specify the minumum C++ standard compilation level. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: eldiener  
> Created_at: 2021-03-14 03:48:39 UTC  
> Url: https://github.com/boostorg/accumulators/pull/46#issuecomment-798827863  

Is there any reason this PR can not be merged to 'develop' or that you can not merge 'develop' to 'master', when the 'master' branch is reopened, for the upcoming Boost 1.76 release ?

---
