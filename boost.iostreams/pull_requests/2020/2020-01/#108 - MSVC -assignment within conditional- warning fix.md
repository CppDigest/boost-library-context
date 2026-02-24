# #108 MSVC "assignment within conditional" warning fix [Merged]

> Username: poelmanc  
> Created at: 2020-01-25 21:01:43 UTC  
> Updated at: 2022-11-02 03:07:11 UTC  
> Merged at: 2022-11-02 03:07:11 UTC  
> Closed at: 2022-11-02 03:07:11 UTC  
> Url: https://github.com/boostorg/iostreams/pull/108  

Unlike gcc/clang, the extra parentheses don't silence MSVC's warning about assignments within conditional statements, and the latest MSVC 2019 issues warnings on these lines. This change performs the assignments prior to the "if" statements.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-01-25 23:02:32 UTC  
> Url: https://github.com/boostorg/iostreams/pull/108#issuecomment-578451205  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/108?src=pr&el=h1) Report  
> Merging [#108](https://codecov.io/gh/boostorg/iostreams/pull/108?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/601244b26aba14a2524ab1307243d0c5ea566206?src=pr&el=desc) will **increase** coverage by `0.02%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/108/graphs/tree.svg?width=650&token=LBEfwtNfca&height=150&src=pr)](https://codecov.io/gh/boostorg/iostreams/pull/108?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #108      +/-   ##  
===========================================  
+ Coverage    68.96%   68.98%   +0.02%       
===========================================  
  Files           80       80                
  Lines         3444     3447       +3       
  Branches      1027     1027                
===========================================  
+ Hits          2375     2378       +3       
  Misses         452      452                
  Partials       617      617  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/108?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/iostreams/filter/newline.hpp](https://codecov.io/gh/boostorg/iostreams/pull/108/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZmlsdGVyL25ld2xpbmUuaHBw) | `90.69% <100%> (+0.16%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/108?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/108?src=pr&el=footer). Last update [601244b...9243a44](https://codecov.io/gh/boostorg/iostreams/pull/108?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: mclow  
> Created_at: 2020-01-26 03:48:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/108#issuecomment-578465929  

I would rather disable the MSVC warning, rather than work around it.

---

## Comment 3

> Username: poelmanc  
> Created_at: 2020-01-26 05:40:47 UTC  
> Url: https://github.com/boostorg/iostreams/pull/108#issuecomment-578470713  

> I would rather disable the MSVC warning, rather than work around it.  
  
Interesting. In the original code `d` is declared uninitialized and assigned within the assignment statement. To me the patch improves those lines of code.  
  
One can debate the clarity of the two `success` assignments within conditionals -- to me assignment within conditional expressions is more handy in `else if` than in `if` -- but I find the new code clearer. The proposed fix takes 6 fewer lines of code than `#ifdef`ed push/disable/pop.  
  
Either solution would be fine with me though!

---
