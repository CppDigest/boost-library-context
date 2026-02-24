# #191 Support a single #pragma without body [Merged]

> Username: jefftrull  
> Created at: 2023-10-24 03:13:19 UTC  
> Updated at: 2024-01-12 16:40:57 UTC  
> Merged at: 2023-10-25 22:32:48 UTC  
> Closed at: 2023-10-25 22:32:48 UTC  
> Url: https://github.com/boostorg/wave/pull/191  

This approach differs from the initial plan - which just returned when an empty pragma was detected - because we still need to fire pragma hooks.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2023-10-24 03:56:40 UTC  
> Url: https://github.com/boostorg/wave/pull/191#issuecomment-1776500633  

If merged this will resolve #188

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2023-10-24 11:37:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/191#pullrequestreview-1694620875  

LGTM, thanks!

---

## Review 3 [Commented]

> Username: rlenhardt  
> Created_at: 2024-01-12 11:06:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/191#pullrequestreview-1817947109  

📁 include/boost/wave/util/cpp_iterator.hpp

```diff
2456 |         }
2457 |     }
2459 |-     expanded.push_back(result_type(T_NEWLINE, "\n", act_token.get_position()));
```

> Username: rlenhardt  
> Created_at: 2024-01-12 11:06:24 UTC  
> Url: https://github.com/boostorg/wave/pull/191#discussion_r1450253001  

Removing the final new line will cause emitted pragmas to miss the final new line and be misintepreted by following compile steps. For example  
```  
#pragma foo  
#define X hello  
X  
```  
will currently get preprocessed to  
```  
#pragma foo#define X hello  
X  
```  
but should be actually  
```  
#pragma foo  
hello  
```  
Shall i add an issue with more details?

> Username: jefftrull  
> Created_at: 2024-01-12 16:40:57 UTC  
> Updated_at: 2024-01-12 16:40:58 UTC  
> Url: https://github.com/boostorg/wave/pull/191#discussion_r1450688570  

Yikes, yes please!


---
