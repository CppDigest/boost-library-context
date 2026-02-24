# #191 adapt/adt: Don't add/remove const on return type of attributes [Merged]

> Username: Flast  
> Created at: 2018-08-02 03:54:04 UTC  
> Updated at: 2018-08-08 23:21:54 UTC  
> Merged at: 2018-08-08 23:21:49 UTC  
> Closed at: 2018-08-08 23:21:49 UTC  
> Url: https://github.com/boostorg/fusion/pull/191  

Boost.TypeOf always deduces the type as un-cv-qualified value type, thus const-ized  
value type is redundant and inhibiting compiler optimization.

---

## Review 1 [Commented]

> Username: daminetreg  
> Created_at: 2018-08-02 08:58:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/191#pullrequestreview-142702083  

📁 include/boost/fusion/adapted/adt/detail/adapt_base.hpp

```diff
 100 |-     >::type const_type;
  86 |+         deduced_attr_type::type type;                                           \
  87 |+     typedef type const_type;
```

> Username: daminetreg  
> Created_at: 2018-08-02 08:58:55 UTC  
> Updated_at: 2018-08-02 08:58:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/191#discussion_r207150771  

Having const_type return a non-const type is an API break no ?

> Username: Flast  
> Created_at: 2018-08-03 00:59:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/191#discussion_r207416425  

It'll break ABI but API is still stable basically.


---

## Comment 2

> Username: Flast  
> Created_at: 2018-08-08 00:36:03 UTC  
> Url: https://github.com/boostorg/fusion/pull/191#issuecomment-411246117  

There is no against? if so I'll merge this.

---

## Comment 3

> Username: djowel  
> Created_at: 2018-08-08 01:11:21 UTC  
> Url: https://github.com/boostorg/fusion/pull/191#issuecomment-411251559  

👍

---

## Comment 4

> Username: daminetreg  
> Created_at: 2018-08-08 22:40:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/191#issuecomment-411576321  

:+1:

---

## Comment 5

> Username: Flast  
> Created_at: 2018-08-08 23:21:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/191#issuecomment-411584965  

Thanks!

---
