# #283 Add "cxxstd" json field [Merged]

> Username: eldiener  
> Created at: 2020-12-31 01:04:34 UTC  
> Updated at: 2020-12-31 13:29:27 UTC  
> Merged at: 2020-12-31 12:07:49 UTC  
> Closed at: 2020-12-31 12:07:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/283  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-12-31 12:07:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/283#issuecomment-752939845  

Thanks Edward: one small point, can you please add "[CI SKIP]" to these PR's as Travis is seriously backed up and these don't touch the code anyway.

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-12-31 13:29:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/283#issuecomment-752959098  

I did not know about [ci skip]. Thanks for alerting me. I should have added this to all my cxxstd PRs.

---
