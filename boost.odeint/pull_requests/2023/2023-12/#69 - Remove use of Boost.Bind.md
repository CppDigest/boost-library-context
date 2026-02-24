# #69 Remove use of Boost.Bind [Merged]

> Username: mborland  
> Created at: 2023-12-22 13:28:34 UTC  
> Updated at: 2024-01-11 05:28:28 UTC  
> Merged at: 2024-01-11 05:28:25 UTC  
> Closed at: 2024-01-11 05:28:25 UTC  
> Url: https://github.com/boostorg/odeint/pull/69  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-12-22 13:51:14 UTC  
> Updated_at: 2024-01-10 11:56:10 UTC  
> Url: https://github.com/boostorg/odeint/pull/69#issuecomment-1867711977  

## [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`f07b2ac`)](https://app.codecov.io/gh/boostorg/odeint/commit/f07b2ac9477acfab2c8158db862e5954bec9231b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 99.43% compared to head [(`e9f1049`)](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 99.43%.  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/odeint/pull/69/graphs/tree.svg?width=650&height=150&src=pr&token=Er6mj5aLOG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #69      +/-   ##  
===========================================  
- Coverage    99.43%   99.43%   -0.01%       
===========================================  
  Files          168      168                
  Lines         7566     7556      -10       
===========================================  
- Hits          7523     7513      -10       
  Misses          43       43                
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...e/boost/numeric/odeint/stepper/adams\_bashforth.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2FkYW1zX2Jhc2hmb3J0aC5ocHA=) | `98.07% <100.00%> (ø)` | |  
| [...numeric/odeint/stepper/adams\_bashforth\_moulton.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2FkYW1zX2Jhc2hmb3J0aF9tb3VsdG9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/numeric/odeint/stepper/adams\_moulton.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2FkYW1zX21vdWx0b24uaHBw) | `100.00% <100.00%> (ø)` | |  
| [...deint/stepper/adaptive\_adams\_bashforth\_moulton.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2FkYXB0aXZlX2FkYW1zX2Jhc2hmb3J0aF9tb3VsdG9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...deint/stepper/base/explicit\_error\_stepper\_base.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2Jhc2UvZXhwbGljaXRfZXJyb3Jfc3RlcHBlcl9iYXNlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [.../stepper/base/explicit\_error\_stepper\_fsal\_base.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2Jhc2UvZXhwbGljaXRfZXJyb3Jfc3RlcHBlcl9mc2FsX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...eric/odeint/stepper/base/explicit\_stepper\_base.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2Jhc2UvZXhwbGljaXRfc3RlcHBlcl9iYXNlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...deint/stepper/base/symplectic\_rkn\_stepper\_base.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2Jhc2Uvc3ltcGxlY3RpY19ya25fc3RlcHBlcl9iYXNlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [...de/boost/numeric/odeint/stepper/bulirsch\_stoer.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2J1bGlyc2NoX3N0b2VyLmhwcA==) | `99.38% <100.00%> (ø)` | |  
| [...umeric/odeint/stepper/bulirsch\_stoer\_dense\_out.hpp](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9udW1lcmljL29kZWludC9zdGVwcGVyL2J1bGlyc2NoX3N0b2VyX2RlbnNlX291dC5ocHA=) | `95.63% <100.00%> (ø)` | |  
| ... and [20 more](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f07b2ac...e9f1049](https://app.codecov.io/gh/boostorg/odeint/pull/69?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-01-11 05:28:17 UTC  
> Url: https://github.com/boostorg/odeint/pull/69#issuecomment-1886270150  

Only CI failure is timeout. Merging

---
