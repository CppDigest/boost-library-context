# #60 Libbacktrace fallback to sym info when debug info is missing or incomplete [Merged]

> Username: driesdeschout  
> Created at: 2018-08-13 13:08:23 UTC  
> Updated at: 2018-08-20 07:25:31 UTC  
> Merged at: 2018-08-20 07:18:19 UTC  
> Closed at: 2018-08-20 07:18:19 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/60  

This adds lookup in symbol table in case libbacktrace is fails to lookup in debug info e.g. in case it is incomplete or stripped away.

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-08-13 13:25:51 UTC  
> Updated_at: 2018-08-20 07:25:31 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/60#issuecomment-412517018  

[![Coverage Status](https://coveralls.io/builds/18564488/badge)](https://coveralls.io/builds/18564488)  
  
Coverage increased (+0.1%) to 93.348% when pulling **7a595a164f2acea6d04fb730a3b66e882ff3109b on driesdeschout:develop** into **819f2b1c861dec7530372a990ecabab7f5fef48f on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2018-08-13 13:25:51 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/60#issuecomment-412517019  

[![Coverage Status](https://coveralls.io/builds/18456226/badge)](https://coveralls.io/builds/18456226)  
  
Coverage increased (+0.1%) to 93.318% when pulling **1289134b6d241573b6bc9df769969ace5f1b73d5 on DriesDeschout:develop** into **819f2b1c861dec7530372a990ecabab7f5fef48f on boostorg:develop**.

---

## Comment 3

> Username: coveralls  
> Created_at: 2018-08-13 13:25:51 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/60#issuecomment-412517020  

[![Coverage Status](https://coveralls.io/builds/18456226/badge)](https://coveralls.io/builds/18456226)  
  
Coverage increased (+0.1%) to 93.318% when pulling **1289134b6d241573b6bc9df769969ace5f1b73d5 on DriesDeschout:develop** into **819f2b1c861dec7530372a990ecabab7f5fef48f on boostorg:develop**.

---

## Review 4 [Changes requested]

> Username: apolukhin  
> Created_at: 2018-08-17 06:19:11 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/stacktrace/pull/60#pullrequestreview-147108876  

Many thanks for the PR!

📁 include/boost/stacktrace/detail/libbacktrace_impls.hpp

```diff
  93 |-             );
 100 |+             ) 
 101 |+             ||
```

> Username: apolukhin  
> Created_at: 2018-08-17 06:18:47 UTC  
> Updated_at: 2018-08-20 07:06:22 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/60#discussion_r210814014  

Looks like the same addition is required for the `name_impl` function.


---

## Comment 5

> Username: apolukhin  
> Created_at: 2018-08-20 07:18:48 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/60#issuecomment-414220344  

Many thanks! Great patch!

---
