# #63 CI Fixes [Merged]

> Username: mborland  
> Created at: 2023-12-15 09:44:40 UTC  
> Updated at: 2023-12-19 07:04:55 UTC  
> Merged at: 2023-12-18 09:35:29 UTC  
> Closed at: 2023-12-18 09:35:29 UTC  
> Url: https://github.com/boostorg/odeint/pull/63  



---

## Comment 1

> Username: mborland  
> Created_at: 2023-12-15 11:07:48 UTC  
> Url: https://github.com/boostorg/odeint/pull/63#issuecomment-1857697727  

@flamefire I generally copied and pasted the boost-ci ci.yml for use here. Any idea why the windows jobs all fail at the setup boost step (e.g.: https://github.com/boostorg/odeint/actions/runs/7221188385/job/19675670439#step:5:76)? The other runs all seem fine.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2023-12-15 12:52:04 UTC  
> Url: https://github.com/boostorg/odeint/pull/63#issuecomment-1857837116  

The issue seems to be the slashes: `xcopy /s /e /q /I D:\a\odeint\odeint libs\numeric/odeint`  
  
This should be `xcopy /s /e /q /I D:\a\odeint\odeint libs\numeric\odeint`.  
  
I'll see how to fix this.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2023-12-15 13:01:16 UTC  
> Url: https://github.com/boostorg/odeint/pull/63#issuecomment-1857848378  

Fixed this in https://github.com/boostorg/boost-ci/commit/71398c6f1039b3084471596aba7fe318ecd90093  
  
You can remove https://github.com/boostorg/odeint/pull/63/commits/829ed9eb96eb48503cd68e79ecf7628f17ca0c54 which changed the wrong xcopy invocation

---

## Comment 4

> Username: mborland  
> Created_at: 2023-12-15 13:02:44 UTC  
> Url: https://github.com/boostorg/odeint/pull/63#issuecomment-1857850119  

> Fixed this in [boostorg/boost-ci@71398c6](https://github.com/boostorg/boost-ci/commit/71398c6f1039b3084471596aba7fe318ecd90093)  
>   
> You can remove [829ed9e](https://github.com/boostorg/odeint/commit/829ed9eb96eb48503cd68e79ecf7628f17ca0c54) which changed the wrong xcopy invocation  
  
Thanks for the quick fix.

---

## Comment 5

> Username: mborland  
> Created_at: 2023-12-15 13:15:01 UTC  
> Url: https://github.com/boostorg/odeint/pull/63#issuecomment-1857865697  

@Flamefire I spoke too soon. The error is now (https://github.com/boostorg/odeint/actions/runs/7222394305/job/19679304353?pr=63#step:5:70):  
  
```  
D:\a\odeint\boost-root>xcopy /s /e /q /I D:\a\odeint\odeint libs\/=\   || EXIT /B 1   
Invalid switch - /=\  
```

---

## Comment 6

> Username: Flamefire  
> Created_at: 2023-12-15 15:20:16 UTC  
> Url: https://github.com/boostorg/odeint/pull/63#issuecomment-1858048594  

Sorry, I forgot about those early expansion rules on Windows CMD. Rerun the Windows jobs now please.

---

## Comment 7

> Username: mborland  
> Created_at: 2023-12-15 16:01:55 UTC  
> Url: https://github.com/boostorg/odeint/pull/63#issuecomment-1858111629  

> Sorry, I forgot about those early expansion rules on Windows CMD. Rerun the Windows jobs now please.  
  
Seems to be working as expected now. Thanks again.

---

## Review 8 [Commented]

> Username: Flamefire  
> Created_at: 2023-12-18 17:35:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/odeint/pull/63#pullrequestreview-1787409696  

📁 include/boost/numeric/odeint/algebra/detail/extract_value_type.hpp

```diff
  44 |+   : mpl::if_< is_same< S, typename S::value_type > ,
  45 |+         mpl::identity< S > , // cut the recursion if S and S::value_type are the same
  46 |+         extract_value_type< typename S::value_type > >::type
```

> Username: Flamefire  
> Created_at: 2023-12-18 17:35:53 UTC  
> Url: https://github.com/boostorg/odeint/pull/63#discussion_r1430464191  

MPL is know to be slow to compile and work was already done to remove dependencies on it but here a new one is introduced, why not use C++11 typetraits (or the boost versions)?  
E.g. something like:  
`conditional<is_same<S, S::value_type>, common_type<S>, extract:value_type<S::value_type>>::type`  
  
(omitted namespaces and `typename` for brevity)

> Username: mborland  
> Created_at: 2023-12-19 07:04:55 UTC  
> Updated_at: 2023-12-19 07:04:56 UTC  
> Url: https://github.com/boostorg/odeint/pull/63#discussion_r1430998825  

I pulled that straight out of a PR that had been sitting here a while. The immediate goal was to get the library correctly working again since Boost.Multiprecision support had been broken for years. Now that everything is working again I am going to spend time deliberately replacing all the C++03 components like MPL (which was already a dependency elsewhere).


---
