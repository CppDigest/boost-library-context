# #110 [skip ci] Add "cxxstd" json field [Merged]

> Username: eldiener  
> Created at: 2021-01-21 04:07:15 UTC  
> Updated at: 2021-01-23 19:05:57 UTC  
> Merged at: 2021-01-23 19:05:57 UTC  
> Closed at: 2021-01-23 19:05:57 UTC  
> Url: https://github.com/boostorg/ublas/pull/110  

The "cxxstd" json field is being added to each Boost library's meta json information for libraries in order to specify the minumum C++ standard compilation level. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: eldiener  
> Created_at: 2021-01-21 04:16:44 UTC  
> Url: https://github.com/boostorg/ublas/pull/110#issuecomment-764249901  

Since this is [skip ci] none of the tests should have been run. Whatever "Clang tidy checks" and "Code Format / Code Formatting Check" are, they have absolutely nothing to do with this PR. Please manually merge and please get rid of these ridiculous tests which are supposedly failing, even when no CI tests should be running at all, with [skip ci].

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-01-21 04:17:14 UTC  
> Updated_at: 2021-01-21 15:27:49 UTC  
> Url: https://github.com/boostorg/ublas/pull/110#issuecomment-764250705  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/110?src=pr&el=h1) Report  
> Merging [#110](https://codecov.io/gh/boostorg/ublas/pull/110?src=pr&el=desc) (5e778b5) into [develop](https://codecov.io/gh/boostorg/ublas/commit/f0e0578b29121a049c6704ffd953a67e6923b484?el=desc) (f0e0578) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/110/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/110?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #110   +/-   ##  
========================================  
  Coverage    94.54%   94.54%             
========================================  
  Files           19       19             
  Lines         1192     1192             
========================================  
  Hits          1127     1127             
  Misses          65       65             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/110?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/110?src=pr&el=footer). Last update [f0e0578...455fb83](https://codecov.io/gh/boostorg/ublas/pull/110?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: sguazt  
> Created_at: 2021-01-21 10:36:41 UTC  
> Url: https://github.com/boostorg/ublas/pull/110#issuecomment-764542442  

Hi,  
Is C++03 really the minimum C++ standard supported?  
I ask this as, according to the prerequisites listed in the uBLAS Wiki (https://github.com/boostorg/ublas/wiki/Guidelines-for-Contribution), it seems that the minimum C++ standard is C++11:  
  
![image](https://user-images.githubusercontent.com/671321/105337062-42675b00-5bda-11eb-8f93-9c319ae62203.png)

---

## Comment 4

> Username: eldiener  
> Created_at: 2021-01-21 15:23:42 UTC  
> Url: https://github.com/boostorg/ublas/pull/110#issuecomment-764718897  

Please fix your "Clang tidy checks" and "Code Format / Code Formatting Check" tests, as they continue to fail. This PR has nothing to do with causing that to happen.

---
