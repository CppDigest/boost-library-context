# #107 View manipulator and is_reflectable trait [Closed]

> Username: denzor200  
> Created at: 2022-09-09 19:45:05 UTC  
> Updated at: 2022-12-16 16:24:43 UTC  
> Closed at: 2022-12-16 16:24:43 UTC  
> Url: https://github.com/boostorg/pfr/pull/107  

https://github.com/boostorg/pfr/issues/100

---

## Comment 1

> Username: Kojoley  
> Created_at: 2022-09-09 20:31:50 UTC  
> Url: https://github.com/boostorg/pfr/pull/107#issuecomment-1242433260  

I don't want to devalue your work, after all thanks for at least trying to work on this. Probably you misunderstood me, it seems that `is_reflectable` is just a placeholder? It actually does nothing by default? Maybe I forgot to show you that there is already in the library an almost working trait https://github.com/boostorg/pfr/blob/b7d839b851dc37506fd6a5a91ab5404e44d6a012/include/boost/pfr/detail/fields_count.hpp#L77-L92 your best bet is to fix some of it's blind spots and it solves the exact problem. Or you have tried to fix it and came to conclusion that it's not possible?   
  
Also, a general rule: the less code is for a review the faster your PR is getting reviewed and more chances for it to be merged in a timely manner.

---

## Review 2 [Commented]

> Username: apolukhin  
> Created_at: 2022-09-10 13:41:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/107#pullrequestreview-1103130358  

A few nitpicks

📁 doc/pfr.qbk

```diff
 412 |+ There are three ways to start using Boost.PFR from other library for type `T` in your code. Each method has its own drawbacks and suits own cases.
 413 |+ 
 414 |+ [table:ops_comp Different approaches for interaction
```

> Username: apolukhin  
> Created_at: 2022-09-10 13:31:25 UTC  
> Updated_at: 2022-09-10 13:41:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/107#discussion_r967653127  

Change the table id to something unique, for example to `table:libs_integration_comp`

---

📁 doc/pfr.qbk

```diff
 442 |+ This method is good if you're writing generic algorithms using a library with Boost.PFR support and need to use reflection from Boost.PFR only if the type can't be recognized by the library:
 443 |+ 
 444 |+ ```
```

> Username: apolukhin  
> Created_at: 2022-09-10 13:35:54 UTC  
> Updated_at: 2022-09-10 13:41:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/107#discussion_r967653543  

Some day this code snippet should be moved into cpp file and compiled like `pfr_sample_adapting`

---

📁 doc/pfr.qbk

```diff
 420 |+         [headerref boost/pfr/view.hpp boost/pfr/view.hpp: view]
 421 |+     ][
 422 |+         Use when you need to access values by library's provided for them reflection or via field-by-field reflection.
```

> Username: apolukhin  
> Created_at: 2022-09-10 13:37:16 UTC  
> Updated_at: 2022-09-10 13:41:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/107#discussion_r967653676  

```diff  
-        Use when you need to access values by library's provided for them reflection or via field-by-field reflection.  
+        Use when you need to access values by other library provided reflection or via field-by-field reflection from Boost.PFR.  
```  
?


---

## Comment 3

> Username: denzor200  
> Created_at: 2022-09-12 06:05:03 UTC  
> Url: https://github.com/boostorg/pfr/pull/107#issuecomment-1243259587  

> Probably you misunderstood me, it seems that `is_reflectable` is just a placeholder? It actually does nothing by default?   
  
I do understand you about your issue automatic `is_reflectable`, this PR won't make to resolve issue impossible, issue will be resolved in the future version of PFR.

---

## Comment 4

> Username: denzor200  
> Created_at: 2022-12-16 16:24:42 UTC  
> Url: https://github.com/boostorg/pfr/pull/107#issuecomment-1355169437  

Not actual. Actual PR here: https://github.com/boostorg/pfr/pull/111

---
