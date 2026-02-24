# #16 [skip ci] Add "cxxstd" json field [Merged]

> Username: eldiener  
> Created at: 2021-01-19 02:22:01 UTC  
> Updated at: 2021-01-19 22:46:55 UTC  
> Merged at: 2021-01-19 22:46:34 UTC  
> Closed at: 2021-01-19 22:46:34 UTC  
> Url: https://github.com/boostorg/align/pull/16  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries in order to specify the minumum C++ standard compilation level. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: glenfe  
> Created_at: 2021-01-19 14:12:44 UTC  
> Url: https://github.com/boostorg/align/pull/16#issuecomment-762864606  

Why is it just two digits, instead of the whole year? i.e. What does "98" refer to after the C++ standard in 2098?

---

## Comment 2

> Username: eldiener  
> Created_at: 2021-01-19 15:52:32 UTC  
> Url: https://github.com/boostorg/align/pull/16#issuecomment-762933987  

The JSON 'cxxstd' field uses the same designations as the Boost Build 'cxxstd' property. Naturally I do not think 0x, 1y, 1z, and perhaps 2a or latest should be used, but the other numbers of 98, 03, 11, 14, 17, 20 seem fine to me. Do you really think that we should worry about the year 2098 yet ?

---

## Comment 3

> Username: glenfe  
> Created_at: 2021-01-19 22:46:55 UTC  
> Url: https://github.com/boostorg/align/pull/16#issuecomment-763192189  

In that case I'm happy to delegate the worry about year 2098 to Boost.Build. :)

---
