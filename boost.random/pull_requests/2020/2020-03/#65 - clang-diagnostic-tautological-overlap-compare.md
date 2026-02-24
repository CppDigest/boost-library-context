# #65 clang-diagnostic-tautological-overlap-compare [Closed]

> Username: crazyhappygame  
> Created at: 2020-03-29 11:56:06 UTC  
> Updated at: 2020-07-07 23:25:26 UTC  
> Closed at: 2020-07-07 23:25:25 UTC  
> Url: https://github.com/boostorg/random/pull/65  

```  
boost/random/linear_congruential.hpp:140:20: error: overlapping comparisons always evaluate to false [clang-diagnostic-tautological-overlap-compare,-warnings-as-errors]  
        if(_x <= 0 && _x != 0) {  
```

---

## Comment 1

> Username: crazyhappygame  
> Created_at: 2020-03-29 11:57:04 UTC  
> Url: https://github.com/boostorg/random/pull/65#issuecomment-605624917  

Could you merge it?

---

## Review 2 [Commented]

> Username: Kojoley  
> Created_at: 2020-04-04 14:24:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/65#pullrequestreview-387703790  

📁 include/boost/random/linear_congruential.hpp

```diff
 139 |         // handle negative seeds
 140 |-         if(_x <= 0 && _x != 0) {
 140 |+         if(_x <= 0) {
```

> Username: Kojoley  
> Created_at: 2020-04-04 14:24:43 UTC  
> Url: https://github.com/boostorg/random/pull/65#discussion_r403475939  

I think you simplified it wrong, it should be `_x < 0` (like the comment above says). Also, there is already a PR #64 for this.


---
