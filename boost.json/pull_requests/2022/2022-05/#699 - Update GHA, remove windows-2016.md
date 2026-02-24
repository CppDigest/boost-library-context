# #699 Update GHA, remove windows-2016 [Merged]

> Username: sdarwin  
> Created at: 2022-05-03 15:28:23 UTC  
> Updated at: 2022-05-14 12:30:02 UTC  
> Merged at: 2022-05-14 12:30:01 UTC  
> Closed at: 2022-05-14 12:30:01 UTC  
> Url: https://github.com/boostorg/json/pull/699  

- Remove windows-2016    
- Add windows-2022  
- Github Actions has been displaying warnings:  
  
```  
Unexpected input(s) 'llvm_version', valid inputs are ['install', 'remove', 'sources', 'toolset', 'version']  
```  
  
because the file .github/actions/setup-packages/action.yml doesn't list llvm_version as an input. It can be corrected with this section.    
  
```  
  llvm_version:  
    description: LLVM version to install  
    required: false  
    default: ''  
```  
  
However, the file action.yml file already has a 'version' input parameter. Why not leverage the existing 'version' variable instead of adding another one. That's what is done in this PR, @grisumbras  if there's a reason to keep 'llvm_version' distinct from 'version' let me know and I'll change it back.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-03 15:49:58 UTC  
> Updated_at: 2022-05-03 15:55:59 UTC  
> Url: https://github.com/boostorg/json/pull/699#issuecomment-1116255470  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/699?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#699](https://codecov.io/gh/boostorg/json/pull/699?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8be6f6b) into [develop](https://codecov.io/gh/boostorg/json/commit/83c364afaf31b58cfe8b97ab159c406c508f47c9?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (83c364a) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/699/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/699?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #699   +/-   ##  
========================================  
  Coverage    99.08%   99.08%             
========================================  
  Files           69       69             
  Lines         6546     6546             
========================================  
  Hits          6486     6486             
  Misses          60       60             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/699?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/699?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [83c364a...8be6f6b](https://codecov.io/gh/boostorg/json/pull/699?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-05-03 16:44:35 UTC  
> Url: https://github.com/boostorg/json/pull/699#issuecomment-1116318666  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/699/pullrequest-condensed-5040d97.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/699/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/699/pullrequest.html)

---
