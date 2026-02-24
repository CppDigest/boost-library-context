# #122 Add "cxxstd" json field. The "cxxstd" json field is being added to ea… [Merged]

> Username: eldiener  
> Created at: 2020-12-16 06:57:58 UTC  
> Updated at: 2020-12-16 08:01:33 UTC  
> Merged at: 2020-12-16 08:01:33 UTC  
> Closed at: 2020-12-16 08:01:33 UTC  
> Url: https://github.com/boostorg/nowide/pull/122  

…ch Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-12-16 07:58:26 UTC  
> Url: https://github.com/boostorg/nowide/pull/122#issuecomment-745866935  

# [Codecov](https://codecov.io/gh/boostorg/nowide/pull/122?src=pr&el=h1) Report  
> Merging [#122](https://codecov.io/gh/boostorg/nowide/pull/122?src=pr&el=desc) (6765594) into [develop](https://codecov.io/gh/boostorg/nowide/commit/dee00fa1dfc11cf2f805afd5a033c05f379ead48?el=desc) (dee00fa) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #122   +/-   ##  
========================================  
  Coverage    89.35%   89.35%             
========================================  
  Files           28       28             
  Lines         2057     2057             
  Branches       185      185             
========================================  
  Hits          1838     1838             
  Misses         213      213             
  Partials         6        6             
```

---
