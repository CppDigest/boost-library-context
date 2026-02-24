# #2124 Avoid the need for floating point arithmetic [Closed]

> Username: nbougalis  
> Created at: 2020-11-20 06:34:18 UTC  
> Updated at: 2020-12-17 08:09:50 UTC  
> Closed at: 2020-12-17 08:09:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2124  

The intent of the code maybe to other, non-integer growth factors (e.g. 1.5 or 2.5) but, as is, the `float` value is both unnecessary and introduces the need for a `static_cast`. Note that this could be further simplified, since `x * 2 - x` will _always_ yield `x`.

---

## Comment 1

> Username: madmongo1  
> Created_at: 2020-11-20 08:18:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2124#issuecomment-731018128  

Thanks for submitting this PR!   
  
One minor pedantic point is that this will fail if the previous size is greater than half the extent of a size_t. Of course it would actually have failed in the original code too.  
  
It might be an idea to at least check for this scenario and clamp the result of the increment.

---

## Comment 2

> Username: nbougalis  
> Created_at: 2020-11-22 23:31:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2124#issuecomment-731865829  

> One minor pedantic point is that this will fail if the previous size is greater than half the extent of a size_t. Of course it would actually have failed in the original code too.  
>   
> It might be an idea to at least check for this scenario and clamp the result of the increment.  
  
Thanks for the comment. I'm happy to add simple overflow detection. I think anything more than overkill, and I suspect that it's highly unlikely for this case to trigger anyways. Let me know if you want to keep it in.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2020-11-28 08:29:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2124#issuecomment-735107425  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2124?src=pr&el=h1) Report  
> Merging [#2124](https://codecov.io/gh/boostorg/beast/pull/2124?src=pr&el=desc) (0a4e30a) into [develop](https://codecov.io/gh/boostorg/beast/commit/1f4561b0c664cc124e2f6c35ec8df7cd04ac6b7c?el=desc) (1f4561b) will **decrease** coverage by `0.01%`.  
> The diff coverage is `83.33%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2124/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/2124?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2124      +/-   ##  
===========================================  
- Coverage    95.07%   95.05%   -0.02%       
===========================================  
  Files          153      153                
  Lines        11959    11961       +2       
===========================================  
  Hits         11370    11370                
- Misses         589      591       +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2124?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/2124/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `97.73% <83.33%> (-0.41%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2124?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2124?src=pr&el=footer). Last update [1f4561b...37fc373](https://codecov.io/gh/boostorg/beast/pull/2124?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
