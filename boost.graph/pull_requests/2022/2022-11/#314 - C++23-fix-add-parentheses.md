# #314 C++23-fix-add-parentheses [Merged]

> Username: poelmanc  
> Created at: 2022-11-22 01:26:59 UTC  
> Updated at: 2022-11-23 00:54:59 UTC  
> Merged at: 2022-11-23 00:54:47 UTC  
> Closed at: 2022-11-23 00:54:47 UTC  
> Url: https://github.com/boostorg/graph/pull/314  

e.g. [a,b] -> [(a,b)] to avoid confusion with C++23 array subscript operator[]  
Without these changes, code produces compilation error with the clangcl 15 delivered by MSVC 2022 15.5 Preview with /std:c++latest mode.

---

## Comment 1

> Username: poelmanc  
> Created_at: 2022-11-22 01:37:52 UTC  
> Updated_at: 2022-11-22 01:38:21 UTC  
> Url: https://github.com/boostorg/graph/pull/314#issuecomment-1322887256  

Footnote: This issue was fixed over in boost/wave already and that author phrased the issue more succinctly:  
  
https://github.com/boostorg/wave/pull/148/commits/ef2d7d830bc7141b7272faf59bd8163db8297f92  
> The comma operator is deprecated inside of square brackets  
> The fix is simple: add a layer of parentheses

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2022-11-22 02:17:27 UTC  
> Url: https://github.com/boostorg/graph/pull/314#issuecomment-1322925130  

Thanks! Once the CI is green, I'll merge it.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2022-11-23 00:54:58 UTC  
> Url: https://github.com/boostorg/graph/pull/314#issuecomment-1324416724  

Thanks, @poelmanc !

---
