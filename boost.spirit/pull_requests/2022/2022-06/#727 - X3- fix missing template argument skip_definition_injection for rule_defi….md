# #727 X3: fix missing template argument skip_definition_injection for rule_defi… [Merged]

> Username: wanghan02  
> Created at: 2022-06-27 11:50:32 UTC  
> Updated at: 2022-06-28 07:45:10 UTC  
> Merged at: 2022-06-27 22:29:54 UTC  
> Closed at: 2022-06-27 22:29:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/727  

template argument `skip_definition_injection` is missing when defining `struct rule_definition` and trying to refer to itself as `Derived` in `parser<Derived>`.

---

## Comment 1

> Username: wanghan02  
> Created_at: 2022-06-27 12:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/727#issuecomment-1167321407  

Hi @Kojoley , I'm not sure if you are still maintaining spirit. Do you know why did my change in "[include/boost/spirit/home/x3/nonterminal/rule.hpp]" break checks for qi and karma?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2022-06-27 22:29:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/727#issuecomment-1167989641  

It's unrelated to your change. Thanks for catching the bug.

---

## Comment 3

> Username: wanghan02  
> Created_at: 2022-06-28 07:45:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/727#issuecomment-1168358587  

> It's unrelated to your change. Thanks for catching the bug.  
  
Thanks!

---
