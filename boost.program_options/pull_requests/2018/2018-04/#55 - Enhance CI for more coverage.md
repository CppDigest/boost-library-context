# #55 Enhance CI for more coverage [Open]

> Username: jeking3  
> Created at: 2018-04-29 12:20:22 UTC  
> Updated at: 2019-02-27 13:50:29 UTC  
> Url: https://github.com/boostorg/program_options/pull/55  



---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-28 15:43:16 UTC  
> Updated_at: 2018-10-28 15:44:04 UTC  
> Url: https://github.com/boostorg/program_options/pull/55#issuecomment-433716644  

Interesting gcc 4.4 failure with expanded coverage  
```  
/usr/include/c++/4.4/bits/shared_ptr.h:1005: error: cannot use typeid with -fno-rtti  
    "g++-4.4"   -std=c++0x -fvisibility-inlines-hidden -fno-rtti -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_NO_RTTI -DBOOST_NO_TYPEID  -I"../.." -c -o "../../bin.v2/libs/program_options/test/options_description_no_rtti_test.test/gcc-4.4/debug/cxxstd-0x-iso/link-static/rtti-off/visibility-hidden/options_description_test.o" "../../libs/program_options/test/options_description_test.cpp"  
...failed gcc.compile.c++ ../../bin.v2/libs/program_options/test/options_description_no_rtti_test.test/gcc-4.4/debug/cxxstd-0x-iso/link-static/rtti-  
```  
  
Probably need to drop gcc 4.4 from the list of compilers tested in expanded CI based on this...

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2019-02-27 13:50:29 UTC  
> Url: https://github.com/boostorg/program_options/pull/55#issuecomment-467868555  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/55?src=pr&el=h1) Report  
> Merging [#55](https://codecov.io/gh/boostorg/program_options/pull/55?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/program_options/commit/d95d31684832075fda04c9fc916f8ec875552763?src=pr&el=desc) will **increase** coverage by `0.07%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/55/graphs/tree.svg?width=650&token=UMdM0EgHK3&height=150&src=pr)](https://codecov.io/gh/boostorg/program_options/pull/55?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #55      +/-   ##  
===========================================  
+ Coverage    49.89%   49.96%   +0.07%       
===========================================  
  Files           23       23                
  Lines         1385     1385                
  Branches       707      707                
===========================================  
+ Hits           691      692       +1       
  Misses         110      110                
+ Partials       584      583       -1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/55?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/program\_options/detail/value\_semantic.hpp](https://codecov.io/gh/boostorg/program_options/pull/55/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvZGV0YWlsL3ZhbHVlX3NlbWFudGljLmhwcA==) | `43.75% <0%> (+1.56%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/55?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/55?src=pr&el=footer). Last update [d95d316...5cdb92e](https://codecov.io/gh/boostorg/program_options/pull/55?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
