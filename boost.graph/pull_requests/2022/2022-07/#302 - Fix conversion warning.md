# #302 Fix conversion warning [Merged]

> Username: sebrockm  
> Created at: 2022-07-17 19:32:20 UTC  
> Updated at: 2022-07-19 00:17:44 UTC  
> Merged at: 2022-07-18 11:13:36 UTC  
> Closed at: 2022-07-18 11:13:37 UTC  
> Url: https://github.com/boostorg/graph/pull/302  

Fixes #293   
  
Replaces `int` with the type returned by `strong_components` which is the value type of the provided component map.  
Also removes unneeded `erase` call.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-07-18 00:30:08 UTC  
> Url: https://github.com/boostorg/graph/pull/302#issuecomment-1186643175  

Hi @sebrockm , this looks great, thanks. Good catch of the redundant `.erase` -- I think a lot of people don't realize that it's an unnecessary step!  
  
One thing -- could you please revert the Clang format commit? I really prefer to keep non-functional changes like formatting out of pull requests completely.

---

## Comment 2

> Username: sebrockm  
> Created_at: 2022-07-18 10:17:04 UTC  
> Url: https://github.com/boostorg/graph/pull/302#issuecomment-1187019651  

@jeremy-murphy Thanks for you reply!  
I fully agree that it's nicer to separate functional and non-functional changes. And of course can I revert it, that's why I made it a separate commit :) But separating it was actually a little effort because my editor automatically applies clang-format on file save.  
  
Would a formatting-only PR make sense? It looks like there are some more files that are not fully in line with clang-format.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2022-07-19 00:17:44 UTC  
> Url: https://github.com/boostorg/graph/pull/302#issuecomment-1188460431  

> Would a formatting-only PR make sense? It looks like there are some more files that are not fully in line with clang-format.  
  
One day, yes, but it's just not a priority at the moment.

---
