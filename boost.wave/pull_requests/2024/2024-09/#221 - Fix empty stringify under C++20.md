# #221 Fix empty stringify under C++20 [Merged]

> Username: jwnhy  
> Created at: 2024-09-11 04:01:54 UTC  
> Updated at: 2024-09-15 05:06:37 UTC  
> Merged at: 2024-09-15 04:42:33 UTC  
> Closed at: 2024-09-15 04:42:33 UTC  
> Url: https://github.com/boostorg/wave/pull/221  

Fix #220.  
  
Check if the variadics variable is omitted; put an empty string if so.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2024-09-12 05:08:45 UTC  
> Url: https://github.com/boostorg/wave/pull/221#issuecomment-2345285052  

Thank you for your issues and PRs, which I am going through slowly :) I sent you a test for one of them.  
Will be traveling for a week so progress will be slow.

---

## Review 2 [Commented]

> Username: jefftrull  
> Created_at: 2024-09-13 02:23:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/221#pullrequestreview-2302010804  

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1270 |+                     // insert a empty string
1271 |+                     expanded.push_back(token_type(T_STRINGLIT, "\"\"", last_valid));
1272 |+                     adjacent_stringize = false;
```

> Username: jefftrull  
> Created_at: 2024-09-13 02:23:00 UTC  
> Url: https://github.com/boostorg/wave/pull/221#discussion_r1758079599  

It seems to me that you could leave this out and let the code fall through to the original `adjacent_stringize = false;`


---

## Review 3 [Commented]

> Username: jefftrull  
> Created_at: 2024-09-13 02:23:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/221#pullrequestreview-2302010950  

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1294 |+                             impl::as_stringlit(arguments[i], pos), pos));
1295 |+                     }
1296 |+                     adjacent_stringize = false;
```

> Username: jefftrull  
> Created_at: 2024-09-13 02:23:12 UTC  
> Url: https://github.com/boostorg/wave/pull/221#discussion_r1758079727  

and this one


---

## Review 4 [Commented]

> Username: jefftrull  
> Created_at: 2024-09-13 02:24:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/221#pullrequestreview-2302011522  

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
1296 |+                     adjacent_stringize = false;
1297 |                 }
1285 |-                 adjacent_stringize = false;
```

> Username: jefftrull  
> Created_at: 2024-09-13 02:24:05 UTC  
> Url: https://github.com/boostorg/wave/pull/221#discussion_r1758080298  

and put this one back in? Three fewer changed lines. Otherwise I'm happy with it.


---

## Comment 5

> Username: jwnhy  
> Created_at: 2024-09-13 04:28:37 UTC  
> Url: https://github.com/boostorg/wave/pull/221#issuecomment-2348010119  

Thank you!  
Have just pushed a fix to put the `adjacent_stringize = false;` back in place.

---

## Comment 6

> Username: jefftrull  
> Created_at: 2024-09-15 05:06:35 UTC  
> Url: https://github.com/boostorg/wave/pull/221#issuecomment-2351373912  

Thank you for this contribution.

---
