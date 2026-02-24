# #946 Specify output location of bench executable [Closed]

> Username: sdarwin  
> Created at: 2023-10-02 22:18:08 UTC  
> Updated at: 2023-10-06 15:09:30 UTC  
> Closed at: 2023-10-06 15:09:29 UTC  
> Url: https://github.com/boostorg/json/pull/946  

This will build the benchmark executable as` ./bin.v2/bench` instead of `bin.v2/libs/json/bench/clang-linux-14/release/cxxstd-20-iso/link-static/bench`. By outputting the exe in the same place each time (similar to how b2 generates it's own file as `./b2`) it is easier to find and won't change later.    
  
It would be more convenient if the output locations were ` ./bin.v2/clang/bench` and ` ./bin.v2/gcc/bench`.  That requires knowing the 'toolset'.  Interestingly from the faqs, "How do I get the current value of feature in Jamfile? This is not possible, since Jamfile does not have "current" value of any feature, be it toolset, build variant or anything else. " $(toolset) doesn't have a value.  We can't define the output location to be  ` ./bin.v2/$(toolset)/bench`  
  
After merging this into develop branch `runbenchmarks.py` will break.   I will need to modify the script, manually copy `bench` into ` ./bin.v2/clang/bench` and ` ./bin.v2/gcc/bench`, and run benchmarks from there.  @grisumbras if it looks ok, go ahead and merge.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-10-02 22:37:54 UTC  
> Updated_at: 2023-10-02 22:38:04 UTC  
> Url: https://github.com/boostorg/json/pull/946#issuecomment-1743867650  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/946?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#946](https://app.codecov.io/gh/boostorg/json/pull/946?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e98089b) into [develop](https://app.codecov.io/gh/boostorg/json/commit/66d925116740c1063eab814be7821fc7611ac480?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (66d9251) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/946/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/946?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #946   +/-   ##  
========================================  
  Coverage    92.96%   92.96%             
========================================  
  Files           85       85             
  Lines         8056     8056             
========================================  
  Hits          7489     7489             
  Misses         567      567             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/946?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/946?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [66d9251...e98089b](https://app.codecov.io/gh/boostorg/json/pull/946?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: sdarwin  
> Created_at: 2023-10-03 21:42:58 UTC  
> Url: https://github.com/boostorg/json/pull/946#issuecomment-1745772866  

https://benchmark.cppalliance.org/ processes commits going back to 2020 in order to compare them. This pull request won't be able to retroactively modify earlier copies of the bench Jamfile. So [runbenchmarks.py](https://github.com/cppalliance/Infrastructure-Docs/blob/master/benchmarks/json/runbenchmarks.py) will maintain it's own copy of the file, and write it to disk each time.  That's in place now.

---

## Comment 3

> Username: grisumbras  
> Created_at: 2023-10-04 14:18:05 UTC  
> Url: https://github.com/boostorg/json/pull/946#issuecomment-1746974419  

Your change will break any multiconfiguration build that builds project `json/bench`, because several targets would try to create the same file.  
  
In #947 I instead added a different target that has to be requested explicitly. The target is `bench-local`. You can get it built with e.g.  
  
```  
b2 toolset=gcc bench//bench-local  
```  
  
Note that for example  
```  
b2 toolset=gcc,clang  
b2 toolset=gcc bench//bench-local  
bench/bench-local bench/data/*  
b2 toolset=clang bench//bench-local  
bench/bench-local bench/data/*  
```  
**will not** work as expected, because `b2 toolset=gcc,clang` builds `bench//bench` for both gcc and clang, then `b2 toolset=gcc bench//bench-local` builds `bench//bench-local` for gcc, and then `b2 toolset=clang bench//bench-local` checks if `bench//bench-local` needs to be updated, sees that it's more recent than its dependency (`bench//bench`) and decides not to update it.  
  
If this is a problem, I can set `bench//bench-local` to always be considered out of date.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2023-10-06 15:09:30 UTC  
> Url: https://github.com/boostorg/json/pull/946#issuecomment-1750857633  

Superseded by the pull request "Targets for benchmarks". Closing.

---
