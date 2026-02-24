# #57 Remove EDG 3.2 workaround [Merged]

> Username: Kojoley  
> Created at: 2019-01-06 14:03:09 UTC  
> Updated at: 2019-01-07 20:03:24 UTC  
> Merged at: 2019-01-07 20:03:08 UTC  
> Closed at: 2019-01-07 20:03:08 UTC  
> Url: https://github.com/boostorg/variant/pull/57  

EDG 3.2 is pretty old (the workaround added 16 years ago).

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-01-06 14:18:27 UTC  
> Updated_at: 2019-01-07 02:28:05 UTC  
> Url: https://github.com/boostorg/variant/pull/57#issuecomment-451745089  

[![Coverage Status](https://coveralls.io/builds/20931819/badge)](https://coveralls.io/builds/20931819)  
  
Coverage remained the same at 95.063% when pulling **06b643df318e4f3050cf5e51a3bdd6cc5c2abff1 on Kojoley:remove-edg-no-sfinae** into **500778bd7b786be24166036821e0f9a46bc939b0 on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-01-06 17:11:17 UTC  
> Url: https://github.com/boostorg/variant/pull/57#issuecomment-451757795  

I think it should be the other way around: all the enable_if must be removed in favor of the `typename Visitor::type`

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-01-06 17:13:51 UTC  
> Url: https://github.com/boostorg/variant/pull/57#issuecomment-451757965  

Hmm, I though it was made to allow `boost::apply_visitor<Visitor const>(...)`.

---

## Review 4 [Changes requested]

> Username: apolukhin  
> Created_at: 2019-01-06 19:02:54 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/variant/pull/57#pullrequestreview-189640434  

📁 include/boost/variant/detail/apply_visitor_binary.hpp

```diff
  22 |- 
  23 |- #if BOOST_WORKAROUND(__EDG__, BOOST_TESTED_AT(302))
  21 | #include <boost/mpl/not.hpp>
```

> Username: apolukhin  
> Created_at: 2019-01-06 19:02:23 UTC  
> Updated_at: 2019-01-06 19:15:51 UTC  
> Url: https://github.com/boostorg/variant/pull/57#discussion_r245518550  

Please, also drop the unnecessary headers.


📁 include/boost/variant/detail/apply_visitor_unary.hpp

```diff
  18 | 
  20 |- #if BOOST_WORKAROUND(__EDG__, BOOST_TESTED_AT(302))
  19 | #include <boost/core/enable_if.hpp>
```

> Username: apolukhin  
> Created_at: 2019-01-06 19:02:49 UTC  
> Updated_at: 2019-01-06 19:15:51 UTC  
> Url: https://github.com/boostorg/variant/pull/57#discussion_r245518563  

Drop those headers too


---

## Comment 5

> Username: apolukhin  
> Created_at: 2019-01-07 20:03:24 UTC  
> Url: https://github.com/boostorg/variant/pull/57#issuecomment-452063410  

Awesome! Many thanks!

---
