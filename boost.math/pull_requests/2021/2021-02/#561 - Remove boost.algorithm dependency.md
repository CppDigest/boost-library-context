# #561 Remove boost.algorithm dependency [Merged]

> Username: mborland  
> Created at: 2021-02-27 12:19:23 UTC  
> Updated at: 2021-02-27 19:33:52 UTC  
> Merged at: 2021-02-27 19:11:23 UTC  
> Closed at: 2021-02-27 19:11:23 UTC  
> Url: https://github.com/boostorg/math/pull/561  

@NAThompson

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-02-27 14:21:48 UTC  
> Updated_at: 2021-02-27 14:37:01 UTC  
> Url: https://github.com/boostorg/math/pull/561#issuecomment-787080164  

@mborland : thanks looks good!  
  
This was really something that should've gone into `std::string` in C++11; looks like it got there in C++20.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-02-27 16:37:48 UTC  
> Url: https://github.com/boostorg/math/pull/561#issuecomment-787099592  

@NAThompson I am not sure why this feature took till C++20 to make it in, but oh well. CI is clean.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-02-27 16:38:48 UTC  
> Url: https://github.com/boostorg/math/pull/561#issuecomment-787099707  

@mborland : Do you think this should go into an anonymous namespace so we don't break anyone else who has hand-coded and `endswith`?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-02-27 16:45:01 UTC  
> Url: https://github.com/boostorg/math/pull/561#issuecomment-787100494  

@NAThompson A "detail" namespace would be better than an anonymous one: anonymous namespaces don't actually hide anything, just change the linkage.

---

## Comment 5

> Username: mborland  
> Created_at: 2021-02-27 16:46:23 UTC  
> Url: https://github.com/boostorg/math/pull/561#issuecomment-787100647  

@jzmaddock Exactly my thought

---

## Comment 6

> Username: mborland  
> Created_at: 2021-02-27 18:25:35 UTC  
> Url: https://github.com/boostorg/math/pull/561#issuecomment-787115179  

@NAThompson I added the detail namespace. Do you think it is worthwhile to bring this version of `ends_with` up into a higher level header in tools (something like adding a boost/math/tools/algorithms.hpp) or fine here since its the only use?

---

## Comment 7

> Username: NAThompson  
> Created_at: 2021-02-27 19:11:09 UTC  
> Url: https://github.com/boostorg/math/pull/561#issuecomment-787120935  

@mborland : Not right now. I believe this is the only thing that could use it. @jzmaddock can overrule at his leisure.

---
